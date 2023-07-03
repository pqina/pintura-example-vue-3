<template>
  <h2>Modal</h2>

  <p>
    <button @click="visible = true">Open editor</button>
  </p>

  <PinturaEditorModal
    v-bind="props"
    v-if="visible"
    :src="src"
    @pintura:hide="visible = false"
    @pintura:show="handleShow()"
    @pintura:close="handleClose()"
    @pintura:load="handleLoad($event)"
    @pintura:process="handleProcess($event)"
  />

  <p v-if="result">
    <img :src="result" alt="" />
  </p>
</template>
<script>
import { PinturaEditorModal } from "@pqina/vue-pintura";
import { getEditorDefaults } from "@pqina/pintura";

// Import Pintura styles
import "@pqina/pintura/pintura.css";

export default {
  name: "ExampleModal",
  components: {
    PinturaEditorModal,
  },
  methods: {
    handleLoad: function (event) {
      console.log("load", event.detail);
    },
    handleProcess: function (event) {
      console.log("process", event.detail);
      this.result = URL.createObjectURL(event.detail.dest);
    },
    handleShow: function () {
      console.log("show");
    },
    handleClose: function () {
      console.log("close");
    },
  },
  data() {
    return {
      props: getEditorDefaults(),
      visible: false,
      src: "image.jpeg",
      result: undefined,
    };
  },
};
</script>

<style>
/* bright / dark mode */
.pintura-editor {
  --color-background: 255, 255, 255;
  --color-foreground: 10, 10, 10;
  box-shadow: 0 0 0 1px rgba(0, 0, 0, 0.1);
}

@media (prefers-color-scheme: dark) {
  html {
    color: #fff;
    background: #111;
  }

  .pintura-editor {
    --color-background: 10, 10, 10;
    --color-foreground: 255, 255, 255;
    box-shadow: 0 0 0 1px rgba(255, 255, 255, 0.1);
  }
}
</style>
