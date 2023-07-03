<template>
  <h2>Example Overlay</h2>

  <p>
    <button v-if="!visible" @click="visible = true">Edit image</button>
    <button v-if="visible" @click="visible = false">Close editor</button>
  </p>

  <p v-if="!visible">
    <img :src="result.imagePreview || src" width="512" height="256" alt="" />
  </p>

  <div v-if="visible" style="width: 512px; height: 256px">
    <PinturaEditorOverlay
      v-bind="props"
      :src="src"
      :imageState="result.imageState"
      @pintura:load="handleLoad($event)"
      @pintura:process="handleProcess($event)"
    />
  </div>
</template>
<script>
import { PinturaEditorOverlay } from "@pqina/vue-pintura";
import { getEditorDefaults } from "@pqina/pintura";

// Import Pintura styles
import "@pqina/pintura/pintura.css";

export default {
  name: "ExampleOverlay",
  components: {
    PinturaEditorOverlay,
  },
  methods: {
    handleLoad: function (event) {
      console.log("load", event.detail);
    },
    handleProcess: function (event) {
      console.log("process", event.detail);
      const { imageState, dest } = event.detail;
      this.result = {
        imagePreview: URL.createObjectURL(dest),
        imageState: imageState,
      };
      this.visible = false;
    },
  },
  data() {
    return {
      props: getEditorDefaults(),
      src: "image.jpeg",
      result: {
        imageState: undefined,
        imagePreview: undefined,
      },
      visible: false,
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
