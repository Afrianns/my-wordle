<script setup>
import { isRef, reactive, ref, toRef, watch } from "vue";
import Swal from "sweetalert2";
import Key from "./Keyboard.vue";

let props = defineProps(["start", "letterlen", "clicked", "letters"]);

import JSConfetti from "js-confetti";

const canvas = document.getElementById("canvas");
const jsConfetti = new JSConfetti({ canvas });

let randomWord = ref([]);

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
let indicatorPass = ref([]);
let gameOver = ref();
let gameOverBanner = ref(false);
let message = ref("");

// start with fetching word & setting the length of the letters
watch(
  () => props.letters,
  (a) => {
    if (a) {
      val.value = props.letterlen;
      gameOverBanner.value = props.clicked;
      randomWord.value = props.letters;
      console.log(randomWord.value.join(""));
    }
  }
);

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
      isCorrect();
    })
    .catch((err) => {
      const Toast = Swal.mixin({
        toast: true,
        position: "top-end",
        showConfirmButton: false,
        timer: 3000,
        background: "#f27474",
        color: "#fff",
        iconColor: "red",
        timerProgressBar: true,
      });

      let msg = "There is an Error, Please Try Again";
      if (err == 404) msg = "Word Cannot be Found";
      Toast.fire({
        icon: "error",
        title: `${msg},<br> Status ${err}`,
      });
    });
  loading.value = false;
  body.classList.remove("overflow");

  if (gameOver.value == val.value) {
    gameOverBanner.value = true;
    message.value = "YOU WIN";
    jsConfetti.addConfetti({
      // emojis: ["🎈", "🧨", "✨", "🎉", "🎊", "🎇", "🌟", "💖"],
      confettiRadius: 5,
      confettiNumber: 100,
    });
  } else if (next.value > 6) {
    message.value = `YOU LOOSE "${randomWord.value
      .join("")
      .toLocaleUpperCase()}"`;
    gameOverBanner.value = true;
  }
};

// check if user already filled 5 letters
let valid = () => {
  if (next.value <= 6 && vals.value[next.value].length === val.value) {
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
    } else if (notPosition(a, b)) {
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
let notPosition = (n, idx) => {
  let res = false;
  // console.log("result" + n + "\nnumber" + idx + "\nword " + randomWord.value);
  randomWord.value.forEach((a, b) => {
    if (a == n && b != idx) {
      res = true;
    }
  });

  return res;
};

// getting keyboard input from user & push it to object
document.addEventListener("keydown", (e) => {
  inputKeys(e.key);
});

function inputKeys(e) {
  let regx = /[A-Za-z]/g;

  if (e == "Backspace" && !loading.value) {
    vals.value[next.value].pop();
  }

  if (e == "Enter" && valid()) {
    NextTries();
  }

  if (
    regx.test(e) &&
    e.length <= 1 &&
    vals.value[next.value].length <= val.value - 1 &&
    !loading.value
  ) {
    vals.value[next.value].push({ col: "", letter: e });
  }

  // console.log(vals.value);
}

let isOver = (n) => {
  if (n) {
    body.classList.add("overflow");
  }
  return n;
};

// function reset all game stuff
let reset = () => {
  vals.value = {
    1: [],
    2: [],
    3: [],
    4: [],
    5: [],
    6: [],
  };
  next.value = 1;
  indicatorPass.value = [{}];
};

let load = () => {
  gameOverBanner.value = false;
};

let virtualKey = (k) => {
  console.log("the key is", k);
  inputKeys(k);
};
</script>

<template id='canvas'>
  <div v-if="isOver(gameOverBanner)" class="game">
    <div class="game-menu">
      <div class="intro">
        <p>
          MI'WORDLE <br />
          <span class="msg">GAME OVER</span>
        </p>
        <h1>{{ message }}</h1>
      </div>
      <button
        @click="
          $emit('startnew');
          reset();
        "
        v-if="!clicked"
      >
        PLAY AGAIN
      </button>
      <button v-else>
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="loading btn-load"
          viewBox="0 0 24 24"
          style="
            transform: rotate(90deg);
            msfilter: progid:DXImageTransform.Microsoft.BasicImage(rotation=1);
          "
        >
          <path
            d="M2 11h5v2H2zm15 0h5v2h-5zm-6 6h2v5h-2zm0-15h2v5h-2zM4.222 5.636l1.414-1.414 3.536 3.536-1.414 1.414zm15.556 12.728-1.414 1.414-3.536-3.536 1.414-1.414zm-12.02-3.536 1.414 1.414-3.536 3.536-1.414-1.414zm7.07-7.071 3.536-3.535 1.414 1.415-3.536 3.535z"
          ></path>
        </svg>
      </button>
      <button
        class="gap"
        @click="
          reset();
          load();
          $emit('startmenu');
        "
      >
        MENU
      </button>
    </div>
  </div>
  <div class="backdrop" v-if="loading">
    <svg
      xmlns="http://www.w3.org/2000/svg"
      class="loading"
      width="40"
      height="40"
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
  </div>
  <!-- <div class="inputLetter">
    <div class="placeholder exist" v-for="(letter, index) in randomWord">
      {{ letter[0] }}
    </div>
  </div> -->
  <div class="inputLetter" v-for="idx in sum">
    <span
      class="placeholder"
      v-for="ind in val"
      :class="vals[idx][ind - 1]?.col"
    >
      {{ vals[idx][ind - 1]?.letter }}
    </span>
  </div>
  <Key class="key" :idx="next" :inp="indicatorPass" @key="virtualKey" />
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
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
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
