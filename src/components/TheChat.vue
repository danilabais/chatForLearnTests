<script setup>
import { ref, onMounted } from "vue";
import throttle from "lodash/throttle";
const chatREF = ref(null);

const isNailed = true;

function getCoords(elem) {
  const box = elem.getBoundingClientRect();
  return {
    top: box.top + pageYOffset,
    left: box.left + pageXOffset,
  };
}
onMounted(() => {
  if (isNailed) chatREF.value.style.bottom = 0;

  chatREF.value.addEventListener("mousedown", (e) => {
    const element = chatREF.value;
    const [elementWidth, elementHeight] = [
      chatREF.value.getBoundingClientRect().width,
      chatREF.value.getBoundingClientRect().height,
    ];
    const pageWidth = document.documentElement.clientWidth;
    const pageHeight = document.documentElement.clientHeight;
    const adhesion = 20;
    if (!e.target.hasAttribute("header")) return;

    const coords = getCoords(element);
    const shiftX = e.pageX - coords.left;
    const shiftY = e.pageY - coords.top;
    const move = (e) => {
      const [offsetX, offsetY] = [e.offsetX, e.offsetY];
      const moveLeft = e.pageX - shiftX;
      const moveTop = e.pageY - shiftY;

      //collision - virtical
      if (moveLeft < 0 + adhesion) {
        chatREF.value.style.left = 0;
      } else chatREF.value.style.left = moveLeft + "px";
      if (moveLeft + elementWidth > pageWidth - adhesion) {
        chatREF.value.style.left = pageWidth - elementWidth + "px";
      }

      //collision - horizontal
      if (moveTop < 0 + adhesion) {
        chatREF.value.style.top = 0;
      } else chatREF.value.style.top = moveTop + "px";

      if (moveTop + elementHeight > pageHeight - adhesion && isNailed) {
        chatREF.value.style.top = pageHeight - elementHeight + "px";
      }
    };
    // const throttledFn = throttle(,200)
    const mousemove = (e) => throttledMove(e);
    const throttledMove = throttle(move, 2);

    window.addEventListener("mousemove", mousemove);

    chatREF.value.addEventListener("mouseup", () => {
      window.removeEventListener("mousemove", mousemove);
    });
  });
});
</script>

<template>
  <div ref="chatREF" class="block">
    <div header class="block__header"></div>
  </div>
</template>

<style lang="scss">
.block {
  background: red;
  width: 400px;
  height: 200px;
  position: absolute;
  border: 2px solid black;

  &__header {
    height: 20%;
    width: 100%;
    background: #000;
  }
}
</style>
