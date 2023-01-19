<script lang="ts" setup>
import { ref, onMounted, onUnmounted } from "vue";
import throttle from "lodash/throttle";
import TheChatBody from "./TheChatBody.vue";

type statusForReturnButtons = null | "left" | "right";

const chatREF = ref<HTMLDivElement | null>(null);

const props = defineProps({
  adhesion: {
    type: Number,
    default: 20,
  },
  nailed: {
    type: Boolean,
    default: false,
  },
});

const PAGE_WIDTH = document.documentElement.clientWidth;
const PAGE_HEIGHT = document.documentElement.clientHeight;

const isHidden = ref<statusForReturnButtons>(null);
let elWidth: number | null = null;
let elHeigth: number | null = null;
const savedCoordsForReturn = { left: 0, top: 0 };

function getCoords(elem: HTMLElement) {
  const box = elem.getBoundingClientRect();
  return {
    elTop: box.top + scrollY,
    elLeft: box.left + scrollX,
  };
}
const getHeigthAndWidth = (element: HTMLElement) => {
  elWidth = element.getBoundingClientRect().width;
  elHeigth = element.getBoundingClientRect().height;
};

const initLogicForInteractive = (e: MouseEvent) => {
  const clickedElemnt = e.target as HTMLDivElement;
  if (!chatREF.value) return;
  const element = chatREF.value;

  if (!clickedElemnt.hasAttribute("header")) return;

  const { elLeft, elTop } = getCoords(element);

  const shiftX = e.pageX - elLeft;
  const shiftY = e.pageY - elTop;
  const move = (e: MouseEvent) => {
    const moveLeft = e.pageX - shiftX;
    const moveTop = e.pageY - shiftY;
    if (!elWidth || !elHeigth)
      return console.log(
        new Error("element width or height not state, check the code")
      );
    //collision - virtical
    if (moveLeft < 0 + props.adhesion) {
      element.style.left = "0";
    } else element.style.left = moveLeft + "px";
    if (moveLeft + elWidth > PAGE_WIDTH - props.adhesion) {
      element.style.left = PAGE_WIDTH - elWidth + "px";
    }

    //collision - horizontal
    if (moveTop < 0 + props.adhesion) {
      element.style.top = "0";
    } else element.style.top = moveTop + "px";

    if (moveTop + elHeigth > PAGE_HEIGHT - props.adhesion) {
      element.style.top = PAGE_HEIGHT - elHeigth + "px";
    }
  };
  const mousemove = (e: MouseEvent) => throttledMove(e);
  const throttledMove = throttle(move, 2);

  window.addEventListener("mousemove", mousemove);

  element.addEventListener("mouseup", () => {
    window.removeEventListener("mousemove", mousemove);
  });
};

onMounted(() => {
  if (!chatREF.value) return console.error(new Error("ChatREF not found"));
  if (props.nailed) chatREF.value.style.bottom = "0";

  const { elLeft, elTop } = getCoords(chatREF.value);

  chatREF.value.style.top = elTop + "px";
  chatREF.value.style.left = elLeft + "px";
  getHeigthAndWidth(chatREF.value);

  chatREF.value.addEventListener("mousedown", initLogicForInteractive);
});

onUnmounted(() => {
  if (!chatREF.value) return;
  chatREF.value.removeEventListener("mousedown", initLogicForInteractive);
});

const close = () => {
  if (!chatREF.value) return console.error(new Error("ChatREF not found"));
  const element = chatREF.value;
  element.removeEventListener("mousedown", initLogicForInteractive);

  const { elLeft, elTop } = getCoords(element);
  savedCoordsForReturn.left = elLeft;
  savedCoordsForReturn.top = elTop;
  console.log(savedCoordsForReturn);

  if (!elWidth || !elHeigth)
    return console.log(
      new Error("element width or height not state, check the code")
    );
  if (elLeft + elWidth / 2 > PAGE_WIDTH / 2) {
    //direction for hide = right
    isHidden.value = "left";
    element.style.transition = "all .3s";
    element.style.left = PAGE_WIDTH + "px";
  } else {
    //direction for hide = left
    isHidden.value = "right";
    element.style.transition = "all .3s";
    element.style.left = -elWidth + "px";
  }
  element.addEventListener("transitionend", () => {
    element.style.transition = "";
  });
};

const returnChat = async () => {
  const element = chatREF.value;
  if (!element) return;
  element.addEventListener("mousedown", initLogicForInteractive);

  isHidden.value = null;
  element.style.transition = "all .3s";
  element.style.left = savedCoordsForReturn.left + "px";
  element.style.top = savedCoordsForReturn.top + "px";
  element.addEventListener("transitionend", () => {
    element.style.transition = "";
  });
};
</script>

<template>
  <div class="wrapper">
    <div ref="chatREF" :class="{ height: !nailed }" class="chat">
      <TheChatBody @close="close" />
      <div
        :class="{ active: isHidden === 'left' }"
        @click="returnChat"
        class="chat__return chat__return--left"
      >
        <img class="chat__return-img" src="@/assets/img/icons/burger.svg" />
      </div>
      <div
        :class="{ active: isHidden === 'right' }"
        @click="returnChat"
        class="chat__return chat__return--right"
      >
        <img class="chat__return-img" src="@/assets/img/icons/burger.svg" />
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.chat {
  border: 1px solid #4d4d4d;
  border-radius: 20px;
  background: #ddd;
  right: 0;
  width: 400px;
  min-height: 400px;
  position: absolute;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: stretch;

  &.height {
    height: 400px;
  }

  &__return {
    position: absolute;
    left: 0;
    width: 50px;
    height: 60px;
    top: 50%;
    background: #ddd;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    opacity: 0;
    transition: all 0.3s;
    &.active {
      opacity: 1;
    }
    &--left {
      border-radius: 20px 0 0 20px;
      transform: translate(-100%, -50%);
    }
    &--right {
      left: 100%;
      border-radius: 0 20px 20px 0;
      transform: translate(0, -50%);
    }
  }
  &__return-img {
    width: 30px;
  }
}
</style>
