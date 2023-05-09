<template>
  <div>
    <h1>Pintura Image Editor</h1>

    <h2>Inline</h2>

    <div style="height: 70vh">
      <PinturaEditor
        v-bind="editorProps"
        :src="inlineSrc"
        @pintura:load="handleInlineLoad($event)"
        @pintura:process="handleInlineProcess($event)"
      />
    </div>

    <p v-if="inlineResult">
      <img :src="inlineResult" alt="" />
    </p>

    <h2>Modal</h2>

    <p>
      <button @click="modalVisible = true">Open editor</button>
    </p>

    <PinturaEditorModal
      v-bind="editorProps"
      v-if="modalVisible"
      :src="modalSrc"
      @pintura:hide="modalVisible = false"
      @pintura:show="handleModalShow()"
      @pintura:close="handleModalClose()"
      @pintura:load="handleModalLoad($event)"
      @pintura:process="handleModalProcess($event)"
    />

    <p v-if="modalResult">
      <img :src="modalResult" alt="" />
    </p>

    <h2>Overlay</h2>

    <p>
      <button v-if="!overlayVisible" @click="overlayVisible = true">
        Edit image
      </button>
      <button v-if="overlayVisible" @click="overlayVisible = false">
        Close editor
      </button>
    </p>

    <p v-if="!overlayVisible">
      <img
        :src="overlayResult.imagePreview || overlaySrc"
        width="512"
        height="256"
        alt=""
      />
    </p>

    <div v-if="overlayVisible" style="width: 512px; height: 256px">
      <PinturaEditorOverlay
        v-bind="editorProps"
        :src="overlaySrc"
        :imageState="overlayResult.imageState"
        @pintura:load="handleOverlayLoad($event)"
        @pintura:process="handleOverlayProcess($event)"
      />
    </div>

    <h2>FilePond</h2>

    <file-pond
      ref="pond"
      server="/api"
      accepted-file-types="image/jpeg, image/png"
      allow-multiple="true"
      :imageEditor="myEditor"
      :files="myFiles"
      @:init="handleFilePondInit"
    />
  </div>
</template>

<script>
// Import Vue FilePond
import vueFilePond from "vue-filepond";
import FilePondPluginFilePoster from "filepond-plugin-file-poster";
import FilePondPluginImageEditor from "@pqina/filepond-plugin-image-editor";

// Import FilePond styles
import "filepond/dist/filepond.min.css";
import "filepond-plugin-file-poster/dist/filepond-plugin-file-poster.min.css";

// vue-pintura
import {
  PinturaEditor,
  PinturaEditorModal,
  PinturaEditorOverlay,
} from "@pqina/vue-pintura";

// pintura
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

  // filepond
  openEditor,
  processImage,
  createDefaultImageOrienter,
  legacyDataToImageState,
} from "@pqina/pintura";

setPlugins(plugin_crop, plugin_finetune, plugin_filter, plugin_annotate);

// Create FilePond component
const FilePond = vueFilePond(
  FilePondPluginImageEditor,
  FilePondPluginFilePoster
);

export default {
  name: "App",
  components: {
    PinturaEditor,
    PinturaEditorModal,
    PinturaEditorOverlay,
    FilePond,
  },
  methods: {
    // inline
    handleInlineLoad: function (event) {
      console.log("inline load", event.detail);
    },
    handleInlineProcess: function (event) {
      console.log("inline process", event.detail);
      this.inlineResult = URL.createObjectURL(event.detail.dest);
    },

    // modal
    handleModalLoad: function (event) {
      console.log("modal load", event.detail);
    },
    handleModalShow: function () {
      console.log("modal show");
    },
    handleModalClose: function () {
      console.log("modal close");
    },
    handleModalProcess: function (event) {
      console.log("modal process", event.detail);
      this.modalResult = URL.createObjectURL(event.detail.dest);
    },

    // overlay
    handleOverlayLoad: function (event) {
      console.log("overlay load", event.detail);
    },
    handleOverlayProcess: function (event) {
      console.log("overlay process", event.detail);
      const { imageState, dest } = event.detail;
      this.overlayResult = {
        imagePreview: URL.createObjectURL(dest),
        imageState: imageState,
      };
      this.overlayVisible = false;
    },

    // filepond
    handleFilePondInit: function () {
      console.log("FilePond has initialized");
      // FilePond instance methods are available on `this.$refs.pond`
    },
  },
  data() {
    return {
      aspectRatio: undefined,
      // defaults
      editorProps: {
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

      // inline state
      inlineSrc: "image.jpeg",
      inlineResult: undefined,

      // modal state
      modalSrc: "image.jpeg",
      modalVisible: false,
      modalResult: undefined,

      // overlay state
      overlaySrc: "image.jpeg",
      overlayVisible: false,
      overlayResult: {
        imageState: undefined,
        imagePreview: undefined,
      },

      // filepond
      myEditor: {
        // map legacy data objects to new imageState objects
        legacyDataToImageState: legacyDataToImageState,

        // used to create the editor, receives editor configuration, should return an editor instance
        createEditor: openEditor,

        // Required, used for reading the image data
        imageReader: [createDefaultImageReader],

        // optionally. can leave out when not generating a preview thumbnail and/or output image
        imageWriter: [createDefaultImageWriter],

        // used to generate poster images, runs an editor in the background
        imageProcessor: processImage,

        // editor options
        editorOptions: {
          imageOrienter: createDefaultImageOrienter(),
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
      },
      myFiles: [],
    };
  },
};
</script>

<style>
@import "../node_modules/@pqina/pintura/pintura.css";

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

html {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

h1 {
  margin-top: 0;
}

body {
  padding: 2em;
}

img {
  max-width: 100%;
}
</style>
