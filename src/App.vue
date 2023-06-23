<script setup>
import { ref, defineProps } from "vue";

import GridContent from "./components/GridContent.vue";
import Nav from "./components/Nav.vue";
import Key from "./components/Keyboard.vue";
let keypress = ref("");
document.addEventListener("keydown", (e) => {
  keypress.value = e.key;
  // console.log("hasil dari app.vue", e.key);
});

setInterval(() => {
  keypress.value = "";
}, 350);

// console.log(keys.keyInput);
</script>

<template>
  <!-- <h1>WORDLE MY CREATION</h1> -->

  <Nav />
  <GridContent />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
