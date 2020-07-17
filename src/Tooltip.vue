<template>
  <v-popover
    v-bind="{
      placement: 'top',
      triggers: ['hover'],
      hoverDelay: delay,
      hoverPopover: hoverTooltip,
      ...$attrs,
    }"
    :triggers="['hover']"
  >
    <slot />

    <template #popover>
      <span class="popover-tooltip">{{ text }}</span>
    </template>
  </v-popover>
</template>

<script>
import Popover from "./Popover";

export default {
  components: { "v-popover": Popover },
  props: {
    text: {
      type: String,
      required: true,
    },
    delay: {
      type: Number,
      default: 0,
    },
    hoverTooltip: {
      type: Boolean,
      default: false,
    },
  },
};
</script>

<style></style>
