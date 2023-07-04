
<script setup>
import { ref } from "vue";
let slide = ref("");
let isPause = ref(false);
let clicked = ref();
let toggle = () => {
  document.documentElement.classList.toggle("main");
  if (!slide.value) {
    slide.value = "btn-slide";
  } else {
    slide.value = "";
  }
};

let pause = () => {
  let body = document.querySelector("body");
  isPause.value = !isPause.value;
  if (isPause.value) {
    body.classList.add("overflow");
  } else {
    body.classList.remove("overflow");
  }
};
</script>
<template>
  <div v-if="isPause" @click.self="pause" class="game">
    <div class="game-menu">
      <div class="intro">
        <p>
          MI'WORDLE <br />
          <span class="msg">PAUSE MODE</span>
        </p>
      </div>
      <button>START OVER</button>
      <button class="gap">MENU</button>
    </div>
  </div>
  <div class="nav">
    <div v-on:click="toggle" class="toggle">
      <span class="btn" v-bind:class="slide"></span>
    </div>
    <div class="nav-menu">
      <p class="surrend">Surrend</p>
      <!-- <span -->
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        stroke-width="1.5"
        stroke="currentColor"
        class="w-6 h-6 pause"
        @click="pause"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          d="M14.25 9v6m-4.5 0V9M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
        />
      </svg>
      <!-- </span> -->
    </div>
  </div>
</template>
<style scoped>
.surrend {
  font-size: 0.75rem;
  text-transform: uppercase;
  border: 1px solid #aaa;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
}
.surrend:hover {
  color: var(--tertiery);
  border-color: var(--tertiery);
}

.pause {
  cursor: pointer;
  fill: transparent;
  width: 30px;
  height: 30px;
}
.pause:hover {
  stroke: var(--tertiery);
}
.nav-menu {
  display: flex;
  gap: 20px;
  align-items: center;
}
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
  color: var(--text);
  width: 30rem;
  padding: 0 1rem;
  /* height: 4rem; */
  border-radius: 10px;
  background-color: var(--primary);
  margin: 0 auto 3rem;
}
</style>