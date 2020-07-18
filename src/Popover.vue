<template>
  <!-- TODO: GET RID OF WRAPPER IN VUE 3 and bind attrs to popover-reference -->
  <div class="popover-wrapper" :class="{ 'popover-wrapper-inline': inline }">
    <component
      :is="tag"
      ref="popoverReference"
      class="popover-reference"
      @mousedown="(ev) => triggerEvent('click', ev)"
      @keydown.enter="(ev) => triggerEvent('enter', ev)"
      @keydown.space="(ev) => triggerEvent('space', ev)"
      @mouseenter="(ev) => triggerEvent('mouseenter', ev)"
      @mouseleave="(ev) => triggerEvent('mouseleave', ev)"
      @focus.capture="(ev) => triggerEvent('focus', ev)"
    >
      <slot />
    </component>

    <div
      v-if="isOpen"
      ref="popoverContent"
      class="popover-content"
      :class="popoverClass"
      @mouseenter="(ev) => triggerEvent('mouseenterpopover', ev)"
      @mouseleave="(ev) => triggerEvent('mouseleavepopover', ev)"
    >
      <slot name="popover" />
      <div v-if="!hideArrow" data-popper-arrow class="popover-arrow">
        <span ref="popoverArrow"></span>
      </div>
    </div>

    <div ref="popover-overlay"></div>
  </div>
</template>

<script>
import { createPopper } from "@popperjs/core";

const stretchModifier = {
  /* https://github.com/popperjs/popper-core/issues/794 */
  name: "stretch",
  enabled: true,
  fn: ({ state, instance }) => {
    const widthOrHeight =
      state.placement.startsWith("left") || state.placement.startsWith("right")
        ? "height"
        : "width";
    const popperSize = state.rects.popper[widthOrHeight];
    const referenceSize = state.rects.reference[widthOrHeight];
    if (Math.floor(popperSize) >= Math.floor(referenceSize)) return;

    state.styles.popper[widthOrHeight] = `${referenceSize}px`;
    instance.update();
  },
  phase: "beforeWrite",
  requires: ["computeStyles"],
};

const rotateArrowmodifier = {
  name: "rotateArrow",
  enabled: true,
  fn: ({ state }) => {
    state.styles.arrow.transform =
      state.styles.arrow.transform + " rotate(45deg)";
  },
  phase: "beforeWrite",
  requires: ["computeStyles"],
};

export default {
  props: {
    triggers: {
      type: Array,
      default: () => [
        "click",
        "enter",
        "space",
        // "hover",
        "focus",
        "clickout",
        "escape",
      ],
    },
    hoverDelay: {
      type: Number,
      default: 150,
    },
    hoverPopover: {
      type: Boolean,
      default: true,
    },
    hideArrow: {
      type: Boolean,
      default: false,
    },
    popoverClass: {
      type: [Object, Array, String],
      default: null,
    },
    show: {
      type: Boolean,
      default: undefined,
    },
    stretch: {
      type: Boolean,
      default: false,
    },
    reference: {
      type: Element,
      default: null,
    },
    inline: {
      type: Boolean,
      default: true,
    },
    tag: {
      type: String,
      default: "div",
    },

    /* Popperjs Options */
    placement: {
      type: String,
      default: "bottom",
      validator: (val) =>
        [
          "auto",
          "auto-start",
          "auto-end",
          "top",
          "top-start",
          "top-end",
          "bottom",
          "bottom-start",
          "bottom-end",
          "right",
          "right-start",
          "right-end",
          "left",
          "left-start",
          "left-end",
        ].includes(val),
    },
    strategy: {
      type: String,
      default: "absolute",
      validator: (val) => ["absolute", "fixed"].includes(val),
    },
    noFlip: {
      type: Boolean,
      default: false,
    },
    flipPadding: {
      type: [Number, Object],
      default: () => 0,
    },
    boundary: {
      type: [String, Element],
      default: "clippingParents",
    },
    offset: {
      type: Number,
      default: 10,
    },
    arrowPadding: {
      type: Number,
      default: 18,
    },
  },
  data() {
    return {
      popperInstance: null,
      isOpen: false,
      lastEvent: null,
    };
  },
  computed: {
    popoverReference() {
      return this.reference || this.$refs.popoverReference;
    },
    popperOptions() {
      const modifiers = [
        {
          name: "flip",
          enabled: !this.noFlip,
          options: {
            padding: this.flipPadding,
            boundary: this.boundary,
          },
        },
        {
          name: "offset",
          options: {
            offset: [0, this.offset],
          },
        },
        {
          name: "arrow",
          options: {
            padding: this.arrowPadding,
          },
        },
        rotateArrowmodifier,
      ];

      this.stretch ? modifiers.push(stretchModifier) : null;

      return {
        placement: this.placement,
        strategy: this.strategy,
        modifiers,
      };
    },
    normalizedTriggers() {
      let normalized = this.triggers.map((t) => {
        if (t === "hover") {
          return this.isTouchDevice()
            ? []
            : [
                "mouseenter",
                "mouseleave",
                ...(this.hoverPopover
                  ? ["mouseenterpopover", "mouseleavepopover"]
                  : []),
              ];
        }
        return t;
      });

      return normalized.flat();
    },
  },
  watch: {
    show: {
      handler(show) {
        if (show === undefined) return;

        show ? this.open() : this.close();
      },
      immediate: true,
    },
  },

  destroyed() {
    this.destroyGlobalListeners();
  },
  methods: {
    async open() {
      if (this.isOpen) return;
      this.isOpen = true;
      await this.$nextTick();

      this.popperInstance = createPopper(
        this.popoverReference,
        this.$refs.popoverContent,
        this.popperOptions
      );

      this.registerGlobalListeners();

      this.$emit("open");
    },
    close() {
      if (!this.isOpen) return;
      this.popperInstance.destroy();
      this.isOpen = false;
      this.destroyGlobalListeners();

      this.$emit("close");
    },
    toggle() {
      this.isOpen ? this.close() : this.open();
    },
    onclick() {
      this.toggle();
    },
    onspace() {
      this.toggle();
    },
    onenter() {
      this.toggle();
    },
    onescape() {
      this.close();
    },
    onclickout() {
      this.close();
    },
    onfocus() {
      this.open();
    },
    onmouseenter() {
      this.withHoverDelay("open");
    },
    onmouseleave() {
      this.withHoverDelay("close");
    },
    onmouseenterpopover() {
      if (!this.hoverPopover) return;
      this.withHoverDelay("open");
    },
    onmouseleavepopover() {
      if (!this.hoverPopover) return;
      this.withHoverDelay("close");
    },
    withHoverDelay(method) {
      clearTimeout(this.hoverDelayTimerId);
      if (this.hoverDelay && this.hoverDelay > 0) {
        this.hoverDelayTimerId = setTimeout(() => {
          clearTimeout(this.hoverDelayTimerId);
          this[method]();
        }, this.hoverDelay);
        return;
      }
      this[method]();
    },

    triggerEvent(type) {
      if (this.lastEvent && this.lastEvent !== "focus") return;

      if (!this.normalizedTriggers.includes(type)) return;

      // if (this.eventIsComingFromPopoverContent(ev) && type !== "escape")
      // return;

      this.lastEvent = type;
      this.eventTimerId = setTimeout(() => {
        clearTimeout(this.eventTimerId);
        const method = `on${this.lastEvent}`;
        this[method]();
        this.lastEvent = null;
      }, 0);
    },
    eventIsComingFromPopoverContent(ev) {
      const popoverContentEl = this.$refs.popoverContent;
      return popoverContentEl && popoverContentEl.contains(ev.target);
    },
    registerGlobalListeners() {
      this.escapeHandler = (ev) => {
        if (ev.key === "Escape") {
          ev.preventDefault();
          this.triggerEvent("escape", ev);
        }
      };

      this.clickOutHandler = (ev) => {
        if (this.eventIsComingFromPopoverContent(ev)) return;
        this.triggerEvent("clickout", ev);
      };

      window.addEventListener("keydown", this.escapeHandler);
      window.addEventListener("mousedown", this.clickOutHandler);
    },
    destroyGlobalListeners() {
      this.escapeHandler &&
        window.removeEventListener("keydown", this.escapeHandler);
      this.clickHandler &&
        window.removeEventListener("mousedown", this.clickOutHandler);
    },
    isTouchDevice() {
      /* https://stackoverflow.com/questions/7838680/detecting-that-the-browser-has-no-mouse-and-is-touch-only/52854585#answer-52854585 */
      return !window.matchMedia("(pointer: fine)").matches;
    },
  },
};
</script>

<style>
/* .popover-wrapper {
  position: relative;
} */

.popover-wrapper-inline {
  display: inline-flex;
}

.popover-arrow {
  width: 10px;
  height: 10px;
  z-index: -1;
  background: inherit;
}

.popover-content[data-popper-placement^="top"] > .popover-arrow {
  bottom: -4px;
}

.popover-content[data-popper-placement^="bottom"] > .popover-arrow {
  top: -4px;
}

.popover-content[data-popper-placement^="left"] > .popover-arrow {
  right: -4px;
}

.popover-content[data-popper-placement^="right"] > .popover-arrow {
  left: -4px;
}
</style>
