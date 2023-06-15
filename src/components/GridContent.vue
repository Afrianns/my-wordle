<script setup>
import { ref } from "vue";

// https://random-word-api.herokuapp.com/word?length=5&lang=en

let result = ref();
let clicked = ref(false);
let val = ref(5);
let sum = ref(6);
let vals = ref({
  1: [],
  2: [],
  3: [],
  4: [],
  5: [],
  6: [],
});

let next = ref(1);

let getSingleWord = async () => {
  try {
    clicked.value = true;

    await fetch("https://random-word-api.vercel.app/api?words=1&length=5")
      .then((response) => response.json())
      .then((response) => {
        result.value = response[0].split("");
        clicked.value = false;
        console.log(response);
      });
  } catch (error) {
    console.log("ada error!" + error);
    clicked.value = false;
    result.value = error;
  }
};

let NextTries = () => {
  next.value++;
};

let valid = () => {
  console.log(vals.value[next.value].length);
  if (next.value < 6 && vals.value[next.value].length === 5) {
    return true;
  }
};

document.addEventListener("keydown", (e) => {
  let regx = /[A-Za-z]/g;

  if (e.key == "Backspace") {
    vals.value[next.value].pop();
  }
  // console.log(vals.value[next.value].push("k"));
  if (
    regx.test(e.key) &&
    e.key.length <= 1 &&
    vals.value[next.value].length <= 4
  ) {
    vals.value[next.value].push(e.key);
  }
});
</script>

<template>
  <box-icon
    v-if="clicked"
    name="loader"
    color="white"
    size="lg"
    animation="spin"
    flip="horizontal"
  ></box-icon>
  <!--
    <input
      type="text"
      v-for="(letter, index) in result"
      maxlength="1"
      class="in"
      disabled
      v-bind:value="letter"
    />
   -->
  <div class="inputLetter">
    <div class="placeholder bg" v-for="(letter, index) in result">
      {{ letter[0] }}
    </div>
  </div>

  <div class="inputLetter" v-for="idx in sum">
    <!-- {{ vals }} -->
    <span class="placeholder" v-for="ind in val">{{ vals[idx][ind - 1] }}</span>
    <!-- <span class="placeholder"></span>
    <span class="placeholder"></span>
    <span class="placeholder"></span>
    <span class="placeholder"></span> -->
  </div>
  <div class="gap">
    <button v-on:click="NextTries" v-if="valid()">Continues</button>
    <button v-on:click="getSingleWord">Click me</button>
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
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

.bg {
  color: #580b0b;
  font-weight: bolder;
  background-color: rgb(238, 222, 199);
}
</style>
