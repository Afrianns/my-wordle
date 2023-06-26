
<script setup>
import { ref } from "vue";
let slide = ref("");

let toggle = () => {
  document.documentElement.classList.toggle("main");
  if (!slide.value) {
    slide.value = "btn-slide";
  } else {
    slide.value = "";
  }
};

// console.log(msg);
</script>
<template>
  <div class="nav">
    <div v-on:click="toggle" class="toggle">
      <span class="btn" v-bind:class="slide"></span>
    </div>
    <span>MI'WORDLE</span>
  </div>
</template>
<style scoped>
.toggle {
  position: relative;
  background-color: var(--toggle);
  width: 3rem;
  border-radius: 20px;
  height: 1.5rem;
  cursor: pointer;
}

.btn {
  transition: 0.3s all cubic-bezier(0.64, 0.11, 0.38, 1.02);
  position: absolute;
  top: 16%;
  left: 10%;
  width: 1rem;
  height: 1rem;
  background: var(--bg);
  border-radius: 50%;
}

.btn-slide {
  transition: 0.3s left cubic-bezier(0.64, 0.11, 0.38, 1.02);
  left: 56%;
}

.nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  /* width: 50%; */
  color: var(--text);
  width: 30rem;
  padding: 0 1rem;
  height: 4rem;
  border-radius: 10px;
  background-color: var(--primary);
  margin: 0 auto 3rem;
}
</style>