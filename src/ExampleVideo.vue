<template>
  <h2>Example Video</h2>

  <p>
    Please note that the video editor extension is only available on the PQINA
    private npm and requires purchasing a license.
  </p>

  <p>
    First set up the <code>.npmrc</code> file and add your private key, then run
    <code>npm install @pqina/pintura-video</code>, and uncomment the
    <a href="https://pqina.nl/pintura/docs/v8/api/video-editor/">
      video extension
    </a>
    related code in the <code>ExampleVideo.vue</code> file.
  </p>

  <div style="height: 70vh">
    <PinturaEditor
      v-bind="props"
      :src="src"
      @pintura:load="handleLoad($event)"
      @pintura:process="handleProcess($event)"
    />
  </div>

  <p v-if="result">
    <video :src="result" controls />
  </p>
</template>
<script>
import { PinturaEditor } from "@pqina/vue-pintura";
import { getEditorDefaults } from "@pqina/pintura";

/*

// Import Pintura Video extension dependencies
import {
  setPlugins,
  createDefaultImageWriter,
  createDefaultMediaWriter,
  imageStateToCanvas,
} from "@pqina/pintura";

import "@pqina/pintura-video/pinturavideo.css";
import {
  plugin_trim_locale_en_gb,
  plugin_trim,
  createDefaultVideoWriter,
  createMediaStreamEncoder,
} from "@pqina/pintura-video";

// Load the Trim plugin view
setPlugins(plugin_trim);

*/

// Import Pintura styles
import "@pqina/pintura/pintura.css";

export default {
  name: "ExampleVideo",
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
      props: getEditorDefaults({
        /*
        util: 'trim',
        locale: {
            // Add the Trim plugin locale
            ...plugin_trim_locale_en_gb,
        },
        imageWriter: createDefaultMediaWriter(
            // Generic Media Writer options, passed to image and video writer
            {
            targetSize: {
                width: 400,
            },
            },
            [
            // For handling images
            createDefaultImageWriter(),

            // For handling videos
            createDefaultVideoWriter({
                // Video writer instructions here
                // ...

                // Encoder to use
                encoder: createMediaStreamEncoder({
                imageStateToCanvas,
                }),
            }),
            ]
        )

        */
      }),
      src: "video.mp4",
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
