<script setup>
import { ref, watch } from "vue";
let props = defineProps(["inp"]);

let keys = ref({
  1: ["q", "w", "e", "r", "t", "y", "u", "i", "o", "p"],
  2: ["a", "s", "d", "f", "g", "h", "j", "k", "l"],
  3: ["del", "z", "x", "c", "v", "b", "n", "m", "enter"],
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
  { key: "Backspace" },
  { key: "z" },
  { key: "x" },
  { key: "c" },
  { key: "v" },
  { key: "b" },
  { key: "n" },
  { key: "m" },
  { key: "Enter" },
]);

watch(
  () => props.inp,
  (a) => {
    if ((a, a.length == 1)) {
    }
    for (let i = 0; i < indicatorKey.value.length - 1; i++) {
      for (let k of a) {
        if (k.key == indicatorKey.value[i].key && a.length > 1) {
          indicatorKey.value[i].indicator = k.indi;
        } else if (a.length == 1) {
          indicatorKey.value[i].indicator = "";
        }
      }
    }
  }
);
</script>
<template>
  <div class="keyboard">
    <div class="group">
      <button
        v-for="(key, ind) in keys[1]"
        :class="indicatorKey[ind].indicator"
        @click="$emit('key', indicatorKey[ind].key)"
        class="btn-key"
      >
        {{ key }}
      </button>
    </div>
    <div class="group">
      <button
        v-for="(key, ind) in keys[2]"
        :class="indicatorKey[ind + 10].indicator"
        @click="$emit('key', indicatorKey[ind + 10].key)"
        class="btn-key"
      >
        {{ key }}
      </button>
    </div>
    <div class="group">
      <!-- <button class="btn-key" @click="$emit('key', 'Backspace')">DEL</button> -->
      <button
        v-for="(key, ind) in keys[3]"
        :class="indicatorKey[ind + 19].indicator"
        @click="$emit('key', indicatorKey[ind + 19].key)"
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
  border: 1px solid transparent;
  padding: 0.1em 1rem;
  gap: 5px;
}

.active {
  border-color: aqua;
}

.used {
  background-color: rgb(183, 183, 183);
  color: #fff;
}

.group:nth-child(2) .btn-key {
  padding: 0.6em 1.4em;
}
.btn-key {
  font-size: 1.3rem;
  text-transform: uppercase;
}

@media screen and (max-width: 800px) {
  #app {
    padding: 0;
  }

  .group:nth-child(2) .btn-key,
  .group .btn-key {
    font-size: 1rem;
    width: 100%;
    padding: 1.5em 1em;
  }

  /* .group {
    gap: 5px;
  } */
}
</style>