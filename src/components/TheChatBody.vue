<script lang="ts" setup>
import { ref } from "vue";
const emit = defineEmits(["close"]);

const messages = ref(0);
setTimeout(() => (messages.value = 20), 1000);
</script>

<template>
  <div header class="chat__header">
    <div class="chat__nick">Sasha Серая</div>
    <button class="chat__header-close" @click="emit('close')">
      <img
        class="chat__header-icon"
        src="@/assets/img/icons/cross.svg"
        alt=""
      />
    </button>
  </div>
  <div class="chat__body">
    <p v-for="item in messages">
      Lorem, ipsum dolor sit amet consectetur adipisicing elit. Quos,
      reprehenderit!
    </p>
  </div>
  <div class="chat__bottom">
    <input maxlength="30" class="chat__bottom-input" type="text" />
    <button class="chat__bottom-btn">
      <img class="chat__bottom-img" src="@/assets/img/icons/send.svg" alt="" />
    </button>
  </div>
</template>

<style scoped lang="scss">
.chat {
  &__header {
    flex: 0 0 60px;
    width: 100%;
    background: rgb(0, 10, 149);
    border-radius: 18px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    user-select: none;
    cursor: move;
  }
  &__bottom-btn {
    border: none;
    background: none;
  }
  &__header-close {
    background: none;
    border: none;
    margin-right: 20px;
    padding: 0;
    cursor: pointer;
  }
  &__bottom-img {
    width: 25px;
    margin-right: 10px;
  }
  &__header-icon {
    width: 30px;
  }
  &__nick {
    margin-left: 20px;
    color: #ddd;
    pointer-events: none;
    font-size: 1.4rem;
  }
  &__bottom {
    background: #ddd;
    border-top: 1px solid #000;
    display: flex;
    flex: 0 0 60px;
    border-radius: 20px;
    overflow: hidden;
  }
  &__bottom-input {
    max-width: 100%;
    flex: auto;
    border: none;
    padding: 0;
    outline: none;
    background: #ddd;
    font-size: 1.6rem;
    padding-right: 10px;
    padding: 10px 20px;
  }
  &__body {
    flex: 0 1 auto;
    overflow: auto;
    padding: 20px;
    display: flex;
    flex-direction: column;
  }
  &__bottom {
    flex: 0 0 auto;
  }
}
</style>
