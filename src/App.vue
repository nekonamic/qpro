<script setup lang="ts">
import { onMounted, ref, watch } from 'vue'

import qproData from './assets/qpro31.json'

import type { qproCode } from './types/qproCode'
import type { spriteItem } from './types/spriteItem'
import type { qproName } from './types/qproName'

import { CopyDocument } from '@element-plus/icons-vue'

const controller = new AbortController();

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

let previewCodeObj: qproCode = {
  face: 0,
  hair: 0,
  head: 0,
  body: 0,
  hand: 0
}

async function handelClick(option: string) {
  qproPreviewList.value = [];

  previewCodeObj = codeObj;
  if (option === "face") {
    for (let i = 0; i < qproData.face.length; i++) {
      let tempCanvas: HTMLCanvasElement = document.createElement('canvas');
      tempCanvas.width = 384;
      tempCanvas.height = 400;
      previewCodeObj.face = i;
      await renderSprites(tempCanvas, previewCodeObj, false);
      await new Promise<void>((resolve) => {
        tempCanvas.toBlob((blob) => {
          if (blob != null) {
            qproPreviewList.value.push(URL.createObjectURL(blob));
          }
          resolve();
        }, 'image/webp');
      });
    }
  }
  if (option === "hair") {
    for (let i = 0; i < qproData.hair.length; i++) {
      let tempCanvas: HTMLCanvasElement = document.createElement('canvas');
      tempCanvas.width = 384;
      tempCanvas.height = 400;
      previewCodeObj.hair = i;
      await renderSprites(tempCanvas, previewCodeObj, false);
      await new Promise<void>((resolve) => {
        tempCanvas.toBlob((blob) => {
          if (blob != null) {
            qproPreviewList.value.push(URL.createObjectURL(blob));
          }
          resolve();
        }, 'image/webp');
      });
    }
  }
  if (option === "head") {
    for (let i = 0; i < qproData.head.length; i++) {
      let tempCanvas: HTMLCanvasElement = document.createElement('canvas');
      tempCanvas.width = 384;
      tempCanvas.height = 400;
      previewCodeObj.head = i;
      await renderSprites(tempCanvas, previewCodeObj, false);
      await new Promise<void>((resolve) => {
        tempCanvas.toBlob((blob) => {
          if (blob != null) {
            qproPreviewList.value.push(URL.createObjectURL(blob));
          }
          resolve();
        }, 'image/webp');
      });
    }
  }
  if (option === "body") {
    for (let i = 0; i < qproData.body.length; i++) {
      let tempCanvas: HTMLCanvasElement = document.createElement('canvas');
      tempCanvas.width = 384;
      tempCanvas.height = 400;
      previewCodeObj.body = i;
      await renderSprites(tempCanvas, previewCodeObj, false);
      await new Promise<void>((resolve) => {
        tempCanvas.toBlob((blob) => {
          if (blob != null) {
            qproPreviewList.value.push(URL.createObjectURL(blob));
          }
          resolve();
        }, 'image/webp');
      });
    }
  }
  if (option === "hand") {
    for (let i = 0; i < qproData.hand.length; i++) {
      let tempCanvas: HTMLCanvasElement = document.createElement('canvas');
      tempCanvas.width = 384;
      tempCanvas.height = 400;
      previewCodeObj.hand = i;
      await renderSprites(tempCanvas, previewCodeObj, false);
      await new Promise<void>((resolve) => {
        tempCanvas.toBlob((blob) => {
          if (blob != null) {
            qproPreviewList.value.push(URL.createObjectURL(blob));
          }
          resolve();
        }, 'image/webp');
      });
    }
  }
}

const qproPreviewList = ref<string[]>([])

const selected = ref()

const qproCanvas = ref<HTMLCanvasElement | null>(null)

onMounted(async () => {
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
</script>

<template>
  <div
    class="rounded-sm border-1 border-solid my-6 mx-36 shadow-xl border-gray-300 bg-white p-4 min-h-[calc(100vh-3rem)] flex items-center justify-center">
    <div class="h-full">
      <canvas ref="qproCanvas" width="384" height="400"></canvas>
      <div class=" space-y-2">
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('face')" class=" w-16">Face</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.face }}</p>
          <el-button type="primary" :icon="CopyDocument">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('hair')" class=" w-16">Hair</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.hair }}</p>
          <el-button type="primary" :icon="CopyDocument">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('head')" class=" w-16">Head</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.head }}</p>
          <el-button type="primary" :icon="CopyDocument">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('body')" class=" w-16">Body</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.body }}</p>
          <el-button type="primary" :icon="CopyDocument">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round @click="handelClick('hand')" class=" w-16">Hand</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.hand }}</p>
          <el-button type="primary" :icon="CopyDocument">Code</el-button>
        </div>
      </div>
    </div>
    <div
      class="w-[calc(384px*2+1rem)] h-[calc(400px*2+1rem)] overflow-y-auto border-l-2 border-dotted border-gray-300 p-2">
      <div class="grid grid-cols-2 gap-2">
        <div v-for="(img, index) in qproPreviewList" :key="index" class="w-[384px] h-[400px] relative border-4"
          :class="selected === index ? 'border-blue-500 rounded-sm' : 'border-transparent'" @click="selected = index">
          <img :src="img" alt="loading" class="object-cover" />
          <div v-if="selected === index"
            class="absolute top-1 right-1 bg-blue-500 text-white text-xs px-2 py-1 rounded-sm">
            Selected
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped></style>