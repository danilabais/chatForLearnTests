<script setup lang="ts">
import TheChat from "./components/TheChat.vue";
import { ref } from "vue";
const ad = ref(10);
const n = ref(false);
// setTimeout(() => (ad.value = 100), 5000);
// setInterval(() => (n.value = !n.value), 4000);
</script>

<template>
  <TheChat :nailed="n" :key="n" :adhesion="ad" />
</template>

<style scoped></style>
