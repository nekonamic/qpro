<script setup lang="ts">
import { onMounted, ref } from 'vue'

import qproData from './assets/qpro31.json'

import type { qproCode } from './types/qproCode'
import type { spriteItem } from './types/spriteItem'
import type { qproName } from './types/qproName'

import { CopyDocument } from '@element-plus/icons-vue'
import { Download } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'

type QproKey = "head" | "hair" | "face" | "hand" | "body";

const queryList: Boolean[] = [];

const spriteData: spriteItem[] = [
  {
    // hair_b
    type: "hair_b",
    image: "",
    position: [256, 0],
    drawAt: [83, 25],
    size: [262, 352],
  },
  {
    // head_b
    type: "head_b",
    image: "",
    position: [512, 0],
    drawAt: [83, 25],
    size: [262, 352],
  },
  {
    // body_b
    type: "body_b",
    image: "",
    position: [256, 0],
    drawAt: [84, 24],
    size: [260, 352],
  },
  {
    // arm_r_upper
    type: "arm_r_upper",
    image: "",
    position: [520, 0],
    drawAt: [81, 181],
    size: [132, 194],
  },
  {
    // arm_r_lower
    type: "arm_r_lower",
    image: "",
    position: [652, 0],
    drawAt: [79, 184],
    size: [132, 194],
  },
  {
    // leg_l_lower
    type: "leg_l_lower",
    image: "",
    position: [662, 326],
    drawAt: [182, 243],
    size: [102, 132],
  },
  {
    // hand_r
    type: "hand_r",
    image: "",
    position: [0, 0],
    drawAt: [39, 27],
    size: [212, 352],
  },
  {
    // leg_r_lower
    type: "leg_r_lower",
    image: "",
    position: [764, 194],
    drawAt: [125, 243],
    size: [102, 132],
  },
  {
    // leg_r_upper
    type: "leg_r_upper",
    image: "",
    position: [662, 194],
    drawAt: [125, 243],
    size: [102, 132],
  },
  {
    // leg_l_upper
    type: "leg_l_upper",
    image: "",
    position: [866, 194],
    drawAt: [182, 243],
    size: [102, 132],
  },
  {
    // body_f
    type: "body_f",
    image: "",
    position: [0, 0],
    drawAt: [84, 24],
    size: [260, 352],
  },
  {
    // arm_l_upper
    type: "arm_l_upper",
    image: "",
    position: [784, 0],
    drawAt: [202, 181],
    size: [142, 194],
  },
  {
    // arm_l_lower
    type: "arm_l_lower",
    image: "",
    position: [520, 194],
    drawAt: [203, 184],
    size: [142, 194],
  },
  {
    // face
    type: "face",
    image: "",
    position: [0, 0],
    drawAt: [140, 61],
    size: [150, 158],
  },
  {
    // hair_f
    type: "hair_f",
    image: "",
    position: [0, 0],
    drawAt: [83, 25],
    size: [262, 352],
  },
  {
    // hand_l
    type: "hand_l",
    image: "",
    position: [212, 0],
    drawAt: [182, 27],
    size: [162, 352],
  },
  {
    // head_f
    type: "head_f",
    image: "",
    position: [0, 0],
    drawAt: [83, 25],
    size: [262, 352],
  },
]

let codeObj: qproCode = {
  face: 0,
  hair: 0,
  head: 0,
  body: 0,
  hand: 0
}

const nameObj = ref<qproName>({
  face: "",
  hair: "",
  head: "",
  body: "",
  hand: ""
})

const queryNow = ref<number>(0)

async function handelClick(option: QproKey) {
  queryList.fill(false);
  let queryNum = queryList.length;
  queryNow.value = queryNum;
  queryList.push(true);
  qproPreviewList.value.push([])

  let previewCodeObj: qproCode = { ...codeObj };
  previewType = option

  for (let i = 0; i < qproData[option].length; i++) {
    let tempCanvas: HTMLCanvasElement = document.createElement('canvas');
    tempCanvas.width = 384;
    tempCanvas.height = 400;
    previewCodeObj[option] = i;
    await renderSprites(tempCanvas, previewCodeObj, false);
    await new Promise<void>((resolve) => {
      tempCanvas.toBlob((blob) => {
        if (blob != null && queryList[queryNum]) {
          qproPreviewList.value[queryNow.value].push(URL.createObjectURL(blob));
        }
        resolve();
      }, 'image/webp');
    });
  }
}

const qproPreviewList = ref<string[][]>([])

let previewType: QproKey

const qproCanvas = ref<HTMLCanvasElement | null>(null)

onMounted(async () => {
  const saved = localStorage.getItem('qproCode')

  if (saved) {
    codeObj = JSON.parse(saved)
  }

  await renderSprites(qproCanvas.value, codeObj, true)
})

async function renderSprites(
  canvas: HTMLCanvasElement | null,
  code: qproCode,
  isPreview: Boolean
): Promise<void> {
  if (!canvas) return

  const ctx = canvas.getContext('2d')
  if (!ctx) return

  let targetQproData
  let targetSpriteData

  // face
  targetQproData = qproData.face.find(item => item.id === code.face)
  if (isPreview) {
    nameObj.value.face = targetQproData!.name
  }
  // face
  targetSpriteData = spriteData.find(item => item.type === "face")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;

  // hair
  targetQproData = qproData.hair.find(item => item.id === code.hair)
  if (isPreview) {
    nameObj.value.hair = targetQproData!.name
  }
  // hair_f
  targetSpriteData = spriteData.find(item => item.type === "hair_f")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // hair_b
  targetSpriteData = spriteData.find(item => item.type === "hair_b")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;

  // head
  targetQproData = qproData.head.find(item => item.id === code.head)
  if (isPreview) {
    nameObj.value.head = targetQproData!.name
  }
  // head_f
  targetSpriteData = spriteData.find(item => item.type === "head_f")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // head_b
  targetSpriteData = spriteData.find(item => item.type === "head_b")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;

  // body
  targetQproData = qproData.body.find(item => item.id === code.body)
  if (isPreview) {
    nameObj.value.body = targetQproData!.name
  }
  // body_f
  targetSpriteData = spriteData.find(item => item.type === "body_f")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // body_b
  targetSpriteData = spriteData.find(item => item.type === "body_b")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // arm_r_upper
  targetSpriteData = spriteData.find(item => item.type === "arm_r_upper")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // arm_r_lower
  targetSpriteData = spriteData.find(item => item.type === "arm_r_lower")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // arm_l_upper
  targetSpriteData = spriteData.find(item => item.type === "arm_l_upper")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // arm_l_lower
  targetSpriteData = spriteData.find(item => item.type === "arm_l_lower")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // leg_r_upper
  targetSpriteData = spriteData.find(item => item.type === "leg_r_upper")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // leg_r_lower
  targetSpriteData = spriteData.find(item => item.type === "leg_r_lower")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // leg_l_upper
  targetSpriteData = spriteData.find(item => item.type === "leg_l_upper")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // leg_l_lower
  targetSpriteData = spriteData.find(item => item.type === "leg_l_lower")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;

  // hand
  targetQproData = qproData.hand.find(item => item.id === code.hand)
  if (isPreview) {
    nameObj.value.hand = targetQproData!.name
  }
  // hand_r
  targetSpriteData = spriteData.find(item => item.type === "hand_r")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;
  // hand_l
  targetSpriteData = spriteData.find(item => item.type === "hand_l")
  targetSpriteData!.image = `/qpro/${targetQproData!.file_name}.webp`;

  for (const sprite of spriteData) {
    const img = await loadImage(sprite.image)

    const [sx, sy] = sprite.position
    const [dx, dy] = sprite.drawAt
    const [dw, dh] = sprite.size

    ctx.drawImage(img, sx, sy, dw, dh, dx, dy, dw, dh)
  }
}

function loadImage(src: string): Promise<HTMLImageElement> {
  return new Promise((resolve, reject) => {
    const img = new Image()
    img.src = src
    img.onload = () => resolve(img)
    img.onerror = reject
  })
}

function copyCode(qproKey: QproKey) {
  navigator.clipboard.writeText(codeObj[qproKey].toString());
  console.log(qproKey)
  ElMessage({
    message: 'Copied successfully',
    type: 'success',
  })
}

function saveImage() {
  const webpDataURL = qproCanvas.value!.toDataURL('image/webp');

  const link = document.createElement('a');
  link.download = `qpro.webp`;
  link.href = webpDataURL;

  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
}

async function changeSprite(code: number) {
  codeObj[previewType] = code;
  localStorage.setItem('qproCode', JSON.stringify(codeObj))
  if (qproCanvas.value) {
    const ctx = qproCanvas.value.getContext('2d');
    if (ctx) {
      ctx.clearRect(0, 0, qproCanvas.value.width, qproCanvas.value.height);
    }
  }
  await renderSprites(qproCanvas.value, codeObj, true)
}
</script>

<template>
  <div
    class="rounded-sm border-1 border-solid my-8 w-fit mx-auto shadow-xl border-gray-300 bg-white p-4 min-h-[calc(100vh-4rem)] flex flex-col md:flex-row items-center justify-center">
    <div class="flex flex-col items-center justify-center">
      <p class="text-2xl font-bold">QPro Previewer for IIDX 31</p>
      <canvas ref="qproCanvas" width="384" height="400" class="w-[70vw] sm:w-[25vw] h-auto"></canvas>
      <div class=" space-y-2 flex flex-col items-center justify-center">
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('face')" class=" w-16">Face</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.face }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('face')">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('hair')" class=" w-16">Hair</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.hair }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('hair')">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('head')" class=" w-16">Head</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.head }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('head')">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('body')" class=" w-16">Body</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.body }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('body')">Code</el-button>
        </div>
        <div class="flex items-center justify-center mb-2">
          <el-button round @click="handelClick('hand')" class=" w-16">Hand</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.hand }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('hand')">Code</el-button>
        </div>
        <el-button type="primary" :icon="Download" @click="saveImage" class="mb-1">Save Qpro As Image</el-button>
        <p class="mb-1">Develop by DJ HITOMI with 🏳️‍⚧️</p>
      </div>
    </div>
    <div class="md:border-l-4 md:h-[80vh] md:mr-4 border-dotted border-gray-300"></div>
    <div class="flex flex-col items-center justify-center">
      <div class="md:border-none border-t-4 border-dotted border-gray-300 w-full"></div>
      <p class="text-2xl">QPro List</p>
      <div
        class="md:w-[calc(50vw+1rem)] md:h-[75vh] w-[70vw] h-[100vh] overflow-y-auto p-1 border-2 border-solid border-gray-300 rounded-sm">
        <div v-if="qproPreviewList[queryNow]" class="grid grid-cols-1 md:grid-cols-2 gap-2">
          <div v-for="(img, index) in qproPreviewList[queryNow]" :key="index"
            class="md:w-[384px] w-full h-auto relative border-4"
            :class="codeObj[previewType] === index ? 'border-blue-500 rounded-sm' : 'border-transparent'"
            @click="codeObj[previewType] = index; changeSprite(index)">
            <img draggable="false" :src="img" alt="loading" class="object-cover" />
            <div v-if="codeObj[previewType] === index"
              class="absolute top-1 right-1 bg-blue-500 text-white text-xs px-2 py-1 rounded-sm">
              Selected
            </div>
          </div>
        </div>
        <div v-else class="flex items-center justify-center h-64">
          <span class="text-gray-500 text-lg">Select a qpro component</span>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>