<script setup>
import { ref } from "vue";
import Swal from "sweetalert2";
import Key from "./Keyboard.vue";

let result = ref();
let clicked = ref(false);
let val = ref(5);
let sum = ref(6);
let loading = ref(false);
let keypress = ref("");
let vals = ref({
  1: [],
  2: [],
  3: [],
  4: [],
  5: [],
  6: [],
});
let keys = ref([]);
let next = ref(1);
let userIn = ref([]);
let randomWord = ref([]);

let indicatorPass = ref([]);

// get random 5 letter 1 word from API
let getSingleWord = async () => {
  clicked.value = true;

  await fetch("https://random-word-api.vercel.app/api?words=1&length=5")
    .then((res) => res.json())
    .then((res) => {
      result.value = res[0].split("");
      clicked.value = false;
      randomWord.value = res[0].split("");
    })
    .catch((error) => {
      console.log("ada error!" + error);
      clicked.value = false;
      result.value = error;
    });
};

// checking if input user are not dummy or random latter/word, and show alert with sweet Alert
let NextTries = async () => {
  let str = "";
  let body = document.querySelector("body");
  loading.value = true;

  body.classList.add("overflow");

  vals.value[next.value].forEach((a, b) => {
    str += a.letter;
  });

  console.log(vals.value[next.value], str);
  await fetch(`https://api.dictionaryapi.dev/api/v2/entries/en/${str}`)
    .then((res) => {
      if (!res.ok) throw res.status;
      return res.json();
    })
    .then((data) => {
      next.value++;
      userIn.value = data[0]["word"].split("");
    })
    .catch((err) => {
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

      let msg = "Terjadi Error, Mohon Coba lagi!";
      if (err == 404) msg = "Kata Tidak ditemukan";
      Toast.fire({
        icon: "error",
        title: `${msg},<br> Status Code ${err}`,
      });
    });
  loading.value = false;
  body.classList.remove("overflow");
  isCorrect();
};

// check if user already filled 5 letters
let valid = () => {
  if (next.value < 6 && vals.value[next.value].length === 5) {
    return true;
  }
};

// check if input user & random word API for every letter are the same
let isCorrect = () => {
  indicatorPass.value = [];
  userIn.value.forEach((a, b) => {
    let indicator;
    if (a == randomWord.value[b]) {
      indicator = "correct";
      indicatorPass.value.push({ indi: indicator, key: a });
    } else {
      indicator = "incorrect";
      indicatorPass.value.push({ indi: indicator, key: a });
    }
    // keys.value.push(a);
    vals.value[next.value - 1][b].col = indicator;
  });
};

// getting keyboard input from user & push it to object
document.addEventListener("keydown", (e) => {
  let regx = /[A-Za-z]/g;

  if (e.key == "Backspace" && !loading.value) {
    vals.value[next.value].pop();
  }
  if (
    regx.test(e.key) &&
    e.key.length <= 1 &&
    vals.value[next.value].length <= 4 &&
    !loading.value
  ) {
    vals.value[next.value].push({ col: "", letter: e.key });
  }
  keypress.value = e.key;
});

setInterval(() => {
  keypress.value = "";
}, 350);
</script>

<template>
  <div class="backdrop" v-if="loading">
    <!-- <box-icon
      class="loading"
      name="loader"
      color="white"
      size="lg"
      animation="spin"
      flip="horizontal"
    ></box-icon> -->
  </div>
  <div class="inputLetter">
    <box-icon
      v-if="clicked"
      name="loader"
      color="white"
      size="lg"
      animation="spin"
      flip="horizontal"
    ></box-icon>
    <div class="placeholder exist" v-for="(letter, index) in result">
      {{ letter[0] }}
    </div>
  </div>
  <div class="inputLetter" v-for="idx in sum">
    <span
      class="placeholder"
      v-for="ind in val"
      :class="vals[idx][ind - 1]?.col"
    >
      {{ vals[idx][ind - 1]?.letter }}
    </span>
  </div>
  <div class="gap">
    <button v-on:click="NextTries" v-if="valid()">Continues</button>
    <button v-on:click="getSingleWord">Click me</button>
  </div>

  <Key :inp="indicatorPass" />
</template>

<style scoped>
.backdrop {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100vh;
  left: 0;
  top: 0;
  position: fixed;
  background: rgba(26, 25, 25, 0.547);
}

.gap > * {
  margin: 1rem;
}

.inputLetter {
  vertical-align: middle;
  justify-content: center;
  margin: 0.5rem auto;
  gap: 0.5rem;
  display: flex;
}
.inputLetter .in {
  text-align: center;
  text-transform: uppercase;
  width: 3rem;
  height: 3rem;
  font-size: 3rem;
  font-weight: bolder;
}

.title-word {
  font-size: 5rem;
  text-transform: uppercase;
}
</style>
