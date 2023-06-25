<script setup>
import { ref, defineProps } from "vue";

import GridContent from "./components/GridContent.vue";
import Nav from "./components/Nav.vue";
let emmit = defineEmits(["GameOver"]);
let keypress = ref("");
let blocks = ref(false);
let startGame = ref(false);

blocks.value = false;
startGame.value = false;

console.log(emmit("GameOver"));

let body = document.querySelector("body");

body.classList.add("overflow");

document.addEventListener("keydown", (e) => {
  keypress.value = e.key;
});

let start = () => {
  blocks.value = true;
  body.classList.remove("overflow");
  startGame.value = true;
};

setInterval(() => {
  keypress.value = "";
}, 350);
</script>
<template>
  <div v-bind:class="{ block: blocks }" class="game">
    <div class="game-menu">
      <div class="intro">
        <p>Welcome to;</p>
        <h1>MI'WORDLE</h1>
      </div>
      <button v-on:click="start()">Mulai</button>
    </div>
  </div>
  <Nav />
  <GridContent :start="startGame" />
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
