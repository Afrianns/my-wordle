<script setup>
import { ref } from "vue";
import Swal from "sweetalert2";
import GridContent from "./components/GridContent.vue";
import Nav from "./components/Nav.vue";

let keypress = ref("");
let blocks = ref(false);
let startGame = ref(false);
let len = ref(5);
let isActive = ref([false, true, false]);

let clicked = ref(false);
let randomWord = ref([]);

blocks.value = false;
startGame.value = false;

let body = document.querySelector("body");
body.classList.add("overflow");

document.addEventListener("keydown", (e) => {
  keypress.value = e.key;
});

// function for length of the letter input option
let lettersLen = (n, i) => {
  if (clicked.value) {
    return;
  }
  isActive.value.forEach((_, k) => {
    isActive.value[k] = false;
  });
  isActive.value[i] = true;
  len.value = n;
};

// get single word with the length of input user from API and pass it to GridContent
let getSingleWord = async () => {
  clicked.value = true;

  await fetch(
    `https://random-word-api.vercel.app/api?words=1&length=${len.value}`
  )
    .then((res) => res.json())
    .then((res) => {
      randomWord.value = res[0].split("");
      body.classList.remove("overflow");
      blocks.value = true;
      startGame.value = true;
      clicked.value = false;
    })
    .catch((error) => {
      console.log("ada error!" + error);

      // Sweet Alert Warning
      const Toast = Swal.mixin({
        toast: true,
        position: "bottom-end",
        showConfirmButton: false,
        timer: 3000,
        background: "#f27474",
        color: "#fff",
        iconColor: "red",
        timerProgressBar: true,
      });

      Toast.fire({
        icon: "error",
        title: `Terjadi Error, Mohon Coba Lagi!,<br>${error}`,
      });
      clicked.value = false;
    });
};

// blocking the home menu
let startmenu = () => {
  blocks.value = false;
};
</script>
<template>
  <div v-bind:class="{ block: blocks }" class="game">
    <div class="game-menu">
      <div class="intro">
        <p>Welcome to;</p>
        <h1>MI'WORDLE</h1>
      </div>
      <div class="letters-wrapper">
        <p>Choose The Length of The Word</p>
        <ul class="letters">
          <li
            class="placeholder"
            :class="{ active: isActive[0] }"
            @click="lettersLen(4, 0)"
          >
            4
          </li>
          <li
            class="placeholder"
            :class="{ active: isActive[1] }"
            @click="lettersLen(5, 1)"
          >
            5
          </li>
          <li
            class="placeholder"
            :class="{ active: isActive[2] }"
            @click="lettersLen(6, 2)"
          >
            6
          </li>
        </ul>
      </div>
      <button v-on:click="getSingleWord()" v-if="!clicked">START</button>
      <button v-else>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="loading"
          width="30"
          height="30"
          viewBox="0 0 24 24"
          style="
            fill: #fff;
            transform: rotate(90deg);
            msfilter: progid:DXImageTransform.Microsoft.BasicImage(rotation=1);
          "
        >
          <path
            d="M2 11h5v2H2zm15 0h5v2h-5zm-6 6h2v5h-2zm0-15h2v5h-2zM4.222 5.636l1.414-1.414 3.536 3.536-1.414 1.414zm15.556 12.728-1.414 1.414-3.536-3.536 1.414-1.414zm-12.02-3.536 1.414 1.414-3.536 3.536-1.414-1.414zm7.07-7.071 3.536-3.535 1.414 1.415-3.536 3.535z"
          ></path>
        </svg>
      </button>
    </div>
  </div>
  <Nav />
  <GridContent
    @startnew="getSingleWord"
    @startmenu="startmenu"
    :letterlen="len"
    :clicked="clicked"
    :letters="randomWord"
    :start="startGame"
  />
</template>

<style scoped>
h1 {
  margin-top: 0;
}
.active {
  border-color: lawngreen;
}
.letters-wrapper {
  padding: 0 0 1.5rem;
}
.letters {
  padding: 0;
  width: 100%;
  list-style: none;
  gap: 10px;
  justify-content: center;
  display: flex;
}
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
