<template>
  <h2>Example</h2>

  <div style="height: 70vh">
    <PinturaEditor
      v-bind="props"
      :src="src"
      @pintura:load="handleLoad($event)"
      @pintura:process="handleProcess($event)"
    />
  </div>

  <p v-if="result">
    <img :src="result" alt="" />
  </p>
</template>
<script>
import { PinturaEditor } from "@pqina/vue-pintura";
import {
  // editor
  createDefaultImageReader,
  createDefaultImageWriter,
  createDefaultShapePreprocessor,
  locale_en_gb,

  // plugins
  setPlugins,
  plugin_crop,
  plugin_crop_locale_en_gb,
  plugin_filter,
  plugin_filter_defaults,
  plugin_filter_locale_en_gb,
  plugin_finetune,
  plugin_finetune_defaults,
  plugin_finetune_locale_en_gb,
  plugin_annotate,
  plugin_annotate_locale_en_gb,
  markup_editor_defaults,
  markup_editor_locale_en_gb,
} from "@pqina/pintura";

// Import Pintura styles
import "@pqina/pintura/pintura.css";

setPlugins(plugin_crop, plugin_finetune, plugin_filter, plugin_annotate);

export default {
  name: "Example",
  components: {
    PinturaEditor,
  },
  methods: {
    handleLoad: function (event) {
      console.log("load", event.detail);
    },
    handleProcess: function (event) {
      console.log("process", event.detail);
      this.result = URL.createObjectURL(event.detail.dest);
    },
  },
  data() {
    return {
      props: {
        utils: ["crop", "finetune", "filter", "annotate"],
        imageReader: createDefaultImageReader(),
        imageWriter: createDefaultImageWriter(),
        shapePreprocessor: createDefaultShapePreprocessor(),
        ...plugin_finetune_defaults,
        ...plugin_filter_defaults,
        ...markup_editor_defaults,
        locale: {
          ...locale_en_gb,
          ...plugin_crop_locale_en_gb,
          ...plugin_finetune_locale_en_gb,
          ...plugin_filter_locale_en_gb,
          ...plugin_annotate_locale_en_gb,
          ...markup_editor_locale_en_gb,
        },
      },
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
