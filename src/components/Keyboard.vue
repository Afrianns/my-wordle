<script setup>
import { onMounted, ref, watch } from "vue";
let props = defineProps(["inp"]);
let clicked = ref([]);

let keys = ref({
  1: ["q", "w", "e", "r", "t", "y", "u", "i", "o", "p"],
  2: ["a", "s", "d", "f", "g", "h", "j", "k", "l"],
  3: ["z", "x", "c", "v", "b", "n", "m", "enter"],
});

let indicatorKey = ref([
  { key: "q" },
  { key: "w" },
  { key: "e" },
  { key: "r" },
  { key: "t" },
  { key: "y" },
  { key: "u" },
  { key: "i" },
  { key: "o" },
  { key: "p" },
  { key: "a" },
  { key: "s" },
  { key: "d" },
  { key: "f" },
  { key: "g" },
  { key: "h" },
  { key: "j" },
  { key: "k" },
  { key: "l" },
  { key: "z" },
  { key: "x" },
  { key: "c" },
  { key: "v" },
  { key: "b" },
  { key: "n" },
  { key: "m" },
  { key: "enter" },
]);

watch(
  () => props.inp,
  (a) => {
    for (let i = 0; i < indicatorKey.value.length - 1; i++) {
      for (let k of a) {
        if (k.key == indicatorKey.value[i].key) {
          indicatorKey.value[i].indicator = k.indi;
        }
      }
    }
  }
);
</script>
<template>
  <div class="keyboard">
    <div class="group">
      <!-- active: props.val == key.key, -->
      <button
        v-for="(key, ind) in keys[1]"
        :class="indicatorKey[ind].indicator"
        class="btn-key"
      >
        {{ key }}
      </button>
    </div>
    <div class="group">
      <button
        v-for="(key, ind) in keys[2]"
        :class="indicatorKey[ind + 10].indicator"
        class="btn-key"
      >
        {{ key }}
      </button>
    </div>
    <div class="group">
      <button class="btn-key">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="ionicon"
          width="40"
          viewBox="0 0 512 512"
        >
          <path
            d="M135.19 390.14a28.79 28.79 0 0021.68 9.86h246.26A29 29 0 00432 371.13V140.87A29 29 0 00403.13 112H156.87a28.84 28.84 0 00-21.67 9.84v0L46.33 256l88.86 134.11z"
            fill="none"
            stroke="currentColor"
            stroke-linejoin="round"
            stroke-width="32"
          />
          <path
            fill="none"
            stroke="currentColor"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="32"
            d="M336.67 192.33L206.66 322.34M336.67 322.34L206.66 192.33M336.67 192.33L206.66 322.34M336.67 322.34L206.66 192.33"
          />
        </svg>
      </button>
      <button
        v-for="(key, ind) in keys[3]"
        :class="indicatorKey[ind + 19].indicator"
        class="btn-key"
      >
        {{ key }}
      </button>
    </div>
  </div>
</template>
<style scoped>
svg {
  padding-top: 0.2rem;
}
.group {
  display: flex;
  justify-content: space-between;
  width: 100%;
  border: 1px solid transparent;
  padding: 0.1em 1em;
  /* transition: border-color 0.25s; */
}

.active {
  border-color: aqua;
}

.used {
  background-color: rgb(183, 183, 183);
  color: #fff;
}

.group:nth-child(1) .btn-key {
  padding: 0.6em 1.55em;
}
.group:nth-child(3) .btn-key {
  padding: 0.2em 1.6em;
}

.group:nth-child(2) .btn-key {
  padding: 0.6em 1.8em;
}

.btn-key {
  font-size: 1.3rem;
  /* margin: .1rem 0 0; */
  text-transform: uppercase;
}
.group .btn-key {
  padding: 0.6em 1.5em;
}
</style>