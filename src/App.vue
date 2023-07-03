<script setup>
import { ref, computed } from "vue";

import Example from "./Example.vue";
import ExampleDefaults from "./ExampleDefaults.vue";
import ExampleModal from "./ExampleModal.vue";
import ExampleFilePond from "./ExampleFilePond.vue";
import ExampleOverlay from "./ExampleOverlay.vue";
import ExampleVideo from "./ExampleVideo.vue";

const routes = {
  "/": Example,
  "/defaults": ExampleDefaults,
  "/modal": ExampleModal,
  "/overlay": ExampleOverlay,
  "/filepond": ExampleFilePond,
  "/video": ExampleVideo,
};

const currentPath = ref(window.location.hash);

window.addEventListener("hashchange", () => {
  currentPath.value = window.location.hash;
});

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || "/"] || NotFound;
});
</script>
<template>
  <h1>Pintura Image Editor</h1>
  <nav>
    <ul>
      <li>
        <a href="#/">Example</a>
      </li>
      <li>
        <a href="#/defaults">Defaults</a>
      </li>
      <li>
        <a href="#/modal">Modal</a>
      </li>
      <li>
        <a href="#/overlay">Overlay</a>
      </li>
      <li>
        <a href="#/filepond">FilePond</a>
      </li>
      <li>
        <a href="#/video">Video</a>
      </li>
    </ul>
  </nav>
  <component :is="currentView" />
</template>
