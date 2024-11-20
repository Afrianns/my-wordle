<script setup>
import { ref, onMounted } from "vue";
let slide = ref("");

onMounted(() => {
  let darkmode = localStorage.getItem('dark');

  if (darkmode != null) {
    console.log(darkmode)
    if (darkmode == 1) {
      console.log('cke', 1)
      slide.value = "btn-slide";
      document.documentElement.classList.add('main');
    }

    if (darkmode == 0) {
      console.log('cke', 0)
      slide.value = "";
      document.documentElement.classList.remove('main');
    }
  } else {
    localStorage.setItem('dark', 0)
  }
})


let toggle = () => {
  if (localStorage.getItem("dark") == 0) {
    slide.value = "btn-slide";
    document.documentElement.classList.add('main');
    localStorage.setItem('dark', 1);
  } else {
    slide.value = "";
    document.documentElement.classList.remove('main');
    localStorage.setItem('dark', 0);
  }
};

// console.log(msg);
</script>
<template>
  <div class="nav">
    <div v-on:click="toggle" class="toggle">
      <span class="btn" v-bind:class="slide"></span>
    </div>
    <span>THE WORDLE GAME</span>
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
  /* width: 100%; */
  padding: 0 1rem;
  height: 4rem;
  border-radius: 10px;
  background-color: var(--primary);
  margin: 1rem auto;
}
</style>