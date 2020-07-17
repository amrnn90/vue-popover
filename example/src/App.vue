<template>
  <div>
    <div class="mt-20">
      <popover
        placement="top-start"
        popover-class="bg-red-500"
        :show="showPopover"
        stretch
        strategy="fixed"
        @open="showPopover = true"
        @close="showPopover = false"
      >
        <button class="w-64 h-64 px-4 py-2 text-white bg-indigo-500 rounded-md">
          Open Me
        </button>

        <template #popover>
          <div class="p-4">
            <input type="text" />
          </div>
        </template>
      </popover>
    </div>
    <div class="mt-32">
      <tooltip text="hola muchas" :delay="200">
        <span class="px-4 py-2 text-white bg-gray-500 rounded-md">
          Tooltipo
        </span>
      </tooltip>
    </div>

    <div class="mt-32">
      <popover
        tag="button"
        popover-class="bg-red-500"
        :reference="$refs.detachedRef"
        placement="top-end"
        stretch
      >
        <button
          class="inline-flex px-4 py-2 text-white bg-indigo-500 rounded-md"
        >
          what does this do?
        </button>

        <template #popover>
          hola mechas
        </template>
      </popover>
    </div>

    <div class="mt-32">
      <div ref="detachedRef" class="inline-flex">
        I am a detached reference
      </div>
    </div>
  </div>
</template>

<script>
// import { Popover, Tooltip } from "../../src";
import { Popover, Tooltip } from "@amrnn/vue-popover";
import "@amrnn/vue-popover/dist/css/styles.css";

export default {
  name: "App",
  components: {
    Popover,
    Tooltip,
  },
  data() {
    return {
      showPopover: true,
    };
  },
  methods: {},
};
</script>

<style>
/* #app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
  max-width: 500px;
  margin: 60px auto;
} */
</style>
