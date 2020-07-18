<template>
  <div style="1000px">
    <div>
      <popover
        placement="top-start"
        popover-class="bg-red-500"
        :show="showPopover"
        stretch
        @open="showPopover = true"
        @close="showPopover = false"
      >
        <button
          class="w-64  h-64 px-4 py-2 text-white bg-indigo-500 rounded-md"
        >
          Open Me
        </button>

        <template #popover>
          <div class="p-4">
            <input type="text" />
          </div>
        </template>
      </popover>
    </div>
    <div class="mt-32"></div>
    <tooltip text="hola muchas" :delay="200">
      <span class="inline-block px-4 py-2 text-white bg-gray-500 rounded-md">
        Tooltipo
      </span>
    </tooltip>

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

    <p>
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Exercitationem
      dignissimos aliquam cumque itaque eaque illo temporibus esse accusantium
      distinctio. Excepturi corporis consequuntur repellat asperiores facilis a
      modi reprehenderit placeat sit itaque ex voluptatibus eveniet accusantium
      veritatis omnis laboriosam, tenetur necessitatibus at libero, et illum
      dignissimos ad eius nobis! Veniam quaerat, deserunt voluptatum rerum
      minima recusandae expedita assumenda dolorem laborum maiores? Praesentium
      minima blanditiis suscipit quod hic atque minus neque quas voluptatibus
      odio accusamus consequuntur ducimus iure, laudantium officia reprehenderit
      explicabo quaerat quibusdam, nulla ullam sed vitae culpa dolores. Veniam,
      consectetur animi. Odit accusantium distinctio excepturi quia sint eos
      quidem placeat!
    </p>
  </div>
</template>

<script>
// import { Popover, Tooltip } from "../../";
import Popover from "../../src/Popover.vue";
import Tooltip from "../../src/Tooltip.vue";
// import { Popover, Tooltip } from "@amrnn/vue-popover";
// import "@amrnn/vue-popover/dist/css/styles.css";

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
