<script setup lang="ts">
import TheChat from "./components/TheChat.vue";
import { ref } from "vue";
const adhesion = ref(10);
const nailed = ref(false);
// setTimeout(() => (ad.value = 100), 5000);
// setInterval(() => (n.value = !n.value), 4000);
</script>

<template>
  <div>
    Включить прилипание? <input v-model="nailed" max="100" type="checkbox" />
  </div>
  <div>
    Сколько пикселей адгезия?
    <input v-model="adhesion" type="number" />
  </div>
  <TheChat :nailed="nailed" :key="String(nailed)" :adhesion="adhesion" />
</template>

<style scoped>
div {
  color: #fff;
  font-size: 1.5rem;
}
</style>
