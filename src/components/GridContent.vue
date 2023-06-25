<script setup>
import { isRef, reactive, ref, toRef, watch } from "vue";
import Swal from "sweetalert2";
import Key from "./Keyboard.vue";

let props = defineProps({ start: Boolean });

let result = ref();
let clicked = ref(false);
let val = ref(5);
let sum = ref(6);
let loading = ref(false);
let vals = ref({
  1: [],
  2: [],
  3: [],
  4: [],
  5: [],
  6: [],
});
let body = document.querySelector("body");
let next = ref(1);
let userIn = ref([]);
let randomWord = ref([]);
let indicatorPass = ref([]);
let gameOver = ref();
let gameOverBanner = ref(false);
let message = ref("KAMU KALAH");

// start with fetching word
watch(
  () => props.start,
  (a) => {
    if (a) {
      getSingleWord();
    }
  }
);

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
  gameOver.value = 0;
  let str = "";
  loading.value = true;

  body.classList.add("overflow");

  vals.value[next.value].forEach((a, b) => {
    str += a.letter;
  });

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
  if (gameOver.value == 5) {
    gameOverBanner.value = true;
    message.value = "KAMU MENANG";
    alert("GAME OVER");
  } else if (next.value > 6) {
    gameOverBanner.value = true;
  }
};

// check if user already filled 5 letters
let valid = () => {
  if (next.value <= 6 && vals.value[next.value].length === 5) {
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
      gameOver.value++;
      indicatorPass.value.push({ indi: indicator, key: a });
    } else if (notPosition(a)) {
      indicator = "notplace";
      indicatorPass.value.push({ indi: indicator, key: a });
    } else {
      indicator = "incorrect";
      indicatorPass.value.push({ indi: indicator, key: a });
    }
    vals.value[next.value - 1][b].col = indicator;
  });
};

// Check if position letter not in the same as random
let notPosition = (n) => {
  let res = false;
  randomWord.value.forEach((a, b) => {
    if (a == n && b != 0) {
      res = true;
    }
  });

  return res;
};

// getting keyboard input from user & push it to object
document.addEventListener("keyup", (e) => {
  let regx = /[A-Za-z]/g;

  if (e.key == "Backspace" && !loading.value) {
    vals.value[next.value].pop();
  }

  if (e.key == "Enter" && valid()) {
    NextTries();
  }

  if (
    regx.test(e.key) &&
    e.key.length <= 1 &&
    vals.value[next.value].length <= 4 &&
    !loading.value
  ) {
    vals.value[next.value].push({ col: "", letter: e.key });
  }
});

let isOver = (n) => {
  if (n) {
    body.classList.add("overflow");
  }
  return n;
};
</script>

<template>
  <div v-if="isOver(gameOverBanner)" class="game">
    <div class="game-menu">
      <div class="intro">
        <p>
          MI'WORDLE <br />
          <span class="msg">GAME OVER</span>
        </p>
        <h1>{{ message }}</h1>
      </div>
      <button v-on:click="$emit()">Kembali</button>
    </div>
  </div>
  <div class="backdrop" v-if="loading">
    <box-icon
      class="loading"
      name="loader"
      color="white"
      size="lg"
      animation="spin"
      flip="horizontal"
    ></box-icon>
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
  <Key class="key" :inp="indicatorPass" />
</template>

<style scoped>
.msg {
  font-size: 1.5rem;
  font-weight: 800;
}
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
  margin: 0.4rem auto;
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

.key {
  margin-top: 4rem;
}

.title-word {
  font-size: 5rem;
  text-transform: uppercase;
}
</style>
