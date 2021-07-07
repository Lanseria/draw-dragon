<template>
  <canvas id="canvas"></canvas>
  <div id="container"></div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from "vue";

export default defineComponent({
  name: "App",
  setup() {
    const imageData = ref<number[]>([]);
    const image = new Image();
    const gap = 6;
    const dragonScale = 2;

    const drawBubble = () => {
      var dragonContainer = document.getElementById("container");

      for (var h = 0; h < image.height; h += gap) {
        for (var w = 0; w < image.width; w += gap) {
          var position = (image.width * h + w) * 4;
          var r = imageData.value[position],
            g = imageData.value[position + 1],
            b = imageData.value[position + 2];

          if (r + g + b <= 160) {
            var bubble = document.createElement("img");
            bubble.src = "bubbles.png";
            bubble.setAttribute("class", "bubble");

            var bubbleSize = Math.random() * 5 + 10;
            bubble.style.left = w * dragonScale - bubbleSize / 2 + "px";
            bubble.style.top = h * dragonScale - bubbleSize / 2 + "px";
            bubble.style.width = bubble.style.height = bubbleSize + "px";
            bubble.style.animationDuration = Math.random() * 6 + 4 + "s";
            bubble.style.position = "absolute";
            if (dragonContainer) {
              dragonContainer.appendChild(bubble);
            }
          }
        }
      }
    };
    const drawDot = () => {
      var canvas = <HTMLCanvasElement>document.getElementById("canvas");
      if (canvas) {
        var ctx = canvas.getContext("2d");

        image.src = "dragon.jpg";
        image.onload = function () {
          canvas.width = image.width;
          canvas.height = image.height;
          if (ctx !== null) {
            ctx.drawImage(image, 0, 0);

            imageData.value = [
              ...ctx.getImageData(0, 0, image.width, image.height).data,
            ] as number[];
            ctx.fillStyle = "#ffffff";
            ctx.fillRect(0, 0, image.width, image.height);

            for (var h = 0; h < image.height; h += gap) {
              for (var w = 0; w < image.width; w += gap) {
                var position = (image.width * h + w) * 4;
                // console.log(position);
                var r = imageData.value[position],
                  g = imageData.value[position + 1],
                  b = imageData.value[position + 2];
                // console.log(r, g, b);
                if (r + g + b <= 150) {
                  ctx.fillStyle = "#000";
                  ctx.fillRect(w, h, 4, 4);
                }
              }
            }

            drawBubble();
          }
        };
      }
    };
    onMounted(() => {
      drawDot();
    });
    return {};
  },
});
</script>
<style scoped>
#container {
  position: relative;
}
</style>
