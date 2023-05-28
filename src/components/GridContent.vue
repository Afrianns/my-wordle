<script setup>
import { ref } from "vue";

defineProps({
  msg: String,
});

// https://random-word-api.herokuapp.com/word?length=5&lang=en

let result = ref();
let clicked = ref(false);

let getSingleWord = async () => {
  try {
    result.value = "";
    clicked.value = true;

    await fetch("https://random-word-api.vercel.app/api?words=1&length=5")
      .then((response) => response.json())
      .then((response) => {
        result.value = response[0];
        clicked.value = false;
        console.log(response);
      });
  } catch (error) {
    console.log("ada error!" + error);
    clicked.value = false;
    result.value = error;
  }
};
</script>

<template>
  <div>
    <box-icon
      v-if="clicked"
      name="loader"
      color="white"
      size="lg"
      animation="spin"
      flip="horizontal"
    ></box-icon>
    <h1 class="title-word">{{ result }}</h1>
  </div>

  <button v-on:click="getSingleWord">Click me</button>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}

.title-word {
  font-size: 5rem;
  text-transform: uppercase;
}
</style>
