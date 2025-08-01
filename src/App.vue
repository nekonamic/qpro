<script setup lang="ts">
import { onMounted, ref } from 'vue'
const spriteData = [
  {
    // hair_b
    image: new URL('./assets/qp/hair.webp', import.meta.url).href,
    position: [256, 0],
    drawAt: [83, 25],
    size: [262, 352],
  },
  {
    // head_b
    image: new URL('./assets/qp/head.webp', import.meta.url).href,
    position: [512, 0],
    drawAt: [83, 25],
    size: [262, 352],
  },
  {
    // body_b
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [256, 0],
    drawAt: [84, 24],
    size: [260, 352],
  },
  {
    // arm_r_upper
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [520, 0],
    drawAt: [81, 181],
    size: [132, 194],
  },
  {
    // arm_r_lower
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [652, 0],
    drawAt: [79, 184],
    size: [132, 194],
  },
  {
    // leg_l_lower
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [662, 326],
    drawAt: [182, 243],
    size: [102, 132],
  },
  {
    // hand_r
    image: new URL('./assets/qp/hand.webp', import.meta.url).href,
    drawAt: [39, 27],
    size: [212, 352],
  },
  {
    // leg_r_lower
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [764, 194],
    drawAt: [125, 243],
    size: [102, 132],
  },
  {
    // leg_r_upper
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [662, 194],
    drawAt: [125, 243],
    size: [102, 132],
  },
  {
    // leg_l_upper
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [866, 194],
    drawAt: [182, 243],
    size: [102, 132],
  },
  {
    // body_f
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    drawAt: [84, 24],
    size: [260, 352],
  },
  {
    // arm_l_upper
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [784, 0],
    drawAt: [202, 181],
    size: [142, 194],
  },
  {
    // arm_l_lower
    image: new URL('./assets/qp/body.webp', import.meta.url).href,
    position: [520, 194],
    drawAt: [203, 184],
    size: [142, 194],
  },
  {
    // face
    image: new URL('./assets/qp/face.webp', import.meta.url).href,
    drawAt: [140, 61],
    size: [150, 158],
  },
  {
    // hair_f
    image: new URL('./assets/qp/hair.webp', import.meta.url).href,
    drawAt: [83, 25],
    size: [262, 352],
  },
  {
    // hand_l
    image: new URL('./assets/qp/hand.webp', import.meta.url).href,
    position: [212, 0],
    drawAt: [182, 27],
    size: [162, 352],
  },
  {
    // head_f
    image: new URL('./assets/qp/head.webp', import.meta.url).href,
    drawAt: [83, 25],
    size: [262, 352],
  },
]

const options = ['Hair', 'Head', 'Body', 'Hand']

const selected = ref('')

const canvasRef = ref<HTMLCanvasElement | null>(null)

onMounted(async () => {
  const canvas = canvasRef.value
  if (!canvas) return

  const ctx = canvas.getContext('2d')
  if (!ctx) return

  for (const sprite of spriteData) {
    const img = await loadImage(sprite.image)

    const [sx, sy] = sprite.position ?? [0, 0]
    const [dx, dy] = sprite.drawAt ?? [0, 0]
    const [dw, dh] = sprite.size ?? [img.width, img.height]

    ctx.drawImage(img, sx, sy, dw, dh, dx, dy, dw, dh)
  }
})

function loadImage(src: string): Promise<HTMLImageElement> {
  return new Promise((resolve, reject) => {
    const img = new Image()
    img.src = src
    img.onload = () => resolve(img)
    img.onerror = reject
  })
}
</script>

<template>
  <div class="border-1 border-solid m-6 shadow-xl border-gray-300 bg-white p-4 min-h-[calc(100vh-3rem)]">
    <div class="flex items-center justify-center min-h-[calc(100vh-5rem-2px)]">
      <div class="h-full">
        <canvas ref="canvasRef" width="384" height="400"></canvas>
      </div>
      <div>
        <div>
          <button>copy</button>
          <p>Face</p>
          <button></button>
        </div>
      </div>
      <div class=" w-[384px] h-[400px] border-1 border-solid">

      </div>
    </div>
  </div>
</template>

<style scoped></style>