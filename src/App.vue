<script setup lang="ts">
import { onMounted, ref } from 'vue'

import qproDataJson from './assets/qpro31_webp.json' with { type: 'json' };

import type { qproCode } from './types/qproCode'
import type { spriteItem } from './types/spriteItem'
import type { qproName } from './types/qproName'

import { CopyDocument } from '@element-plus/icons-vue'
import { Download } from '@element-plus/icons-vue'

type QproDataL2 = {
  id: number;
  name: string;
  file_name: string;
  webp_base64: string
};

type QproDataL1 = {
  head: QproDataL2[];
  hair: QproDataL2[];
  face: QproDataL2[];
  hand: QproDataL2[];
  body: QproDataL2[];
};

let setsCode: qproCode[] = []

let chooseSet:boolean = false

const renderProgressPercentage = ref<number>(0)

const qproData = qproDataJson as QproDataL1;

type QproKey = "head" | "hair" | "face" | "hand" | "body";

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

const qproPreviewList = ref<string[]>([])

let previewType: QproKey

const qproCanvas = ref<HTMLCanvasElement | null>(null)

let tempCanvas: HTMLCanvasElement = document.createElement('canvas');
tempCanvas.width = 384;
tempCanvas.height = 400;

const rending = ref<Boolean>(false)

async function handelClick(option: QproKey) {
  chooseSet = false
  renderProgressPercentage.value = 0;
  const totalRendingQuery = qproData[option].length
  let RendedQuery = 0
  qproPreviewList.value.length = 0;
  rending.value = true
  let previewCodeObj: qproCode = { ...codeObj };
  previewType = option

  for (let i = 0; i < qproData[option].length; i++) {
    previewCodeObj[option] = i;
    await renderSprites(tempCanvas, previewCodeObj, option);
    await new Promise<void>((resolve) => {
      tempCanvas.toBlob((blob) => {
        if (blob != null) {
          qproPreviewList.value.push(URL.createObjectURL(blob));
        }
        resolve();
      }, 'image/webp');
    });
    RendedQuery++
    renderProgressPercentage.value = Math.trunc(RendedQuery / totalRendingQuery * 100)
  }
  rending.value = false
}

onMounted(async () => {
  setsCode = extractCompleteQproCodes()

  const saved = localStorage.getItem('qproCode')

  if (saved) {
    codeObj = JSON.parse(saved)
  }

  await renderSprites(qproCanvas.value, codeObj)
})

async function renderSprites(
  canvas: HTMLCanvasElement | null,
  code: qproCode,
  type?: QproKey,
  isRendingSet?:boolean
): Promise<void> {
  if (!canvas) return

  const ctx = canvas.getContext('2d')
  if (!ctx) return

  ctx.clearRect(0, 0, canvas.width, canvas.height);

  let targetQproData
  let targetSpriteData

  if (type === undefined) {
    // face
    targetQproData = qproData.face.find(item => item.id === code.face)
    if (!isRendingSet) nameObj.value.face = targetQproData!.name;
    // face
    targetSpriteData = spriteData.find(item => item.type === "face")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // hair
    targetQproData = qproData.hair.find(item => item.id === code.hair)
    if (!isRendingSet) nameObj.value.hair = targetQproData!.name
    // hair_f
    targetSpriteData = spriteData.find(item => item.type === "hair_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hair_b
    targetSpriteData = spriteData.find(item => item.type === "hair_b")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // head
    targetQproData = qproData.head.find(item => item.id === code.head)
    if (!isRendingSet) nameObj.value.head = targetQproData!.name
    // head_f
    targetSpriteData = spriteData.find(item => item.type === "head_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // head_b
    targetSpriteData = spriteData.find(item => item.type === "head_b")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // body
    targetQproData = qproData.body.find(item => item.id === code.body)
    if (!isRendingSet) nameObj.value.body = targetQproData!.name
    // body_f
    targetSpriteData = spriteData.find(item => item.type === "body_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // body_b
    targetSpriteData = spriteData.find(item => item.type === "body_b")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // arm_r_upper
    targetSpriteData = spriteData.find(item => item.type === "arm_r_upper")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // arm_r_lower
    targetSpriteData = spriteData.find(item => item.type === "arm_r_lower")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // arm_l_upper
    targetSpriteData = spriteData.find(item => item.type === "arm_l_upper")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // arm_l_lower
    targetSpriteData = spriteData.find(item => item.type === "arm_l_lower")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // leg_r_upper
    targetSpriteData = spriteData.find(item => item.type === "leg_r_upper")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // leg_r_lower
    targetSpriteData = spriteData.find(item => item.type === "leg_r_lower")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // leg_l_upper
    targetSpriteData = spriteData.find(item => item.type === "leg_l_upper")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // leg_l_lower
    targetSpriteData = spriteData.find(item => item.type === "leg_l_lower")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // hand
    targetQproData = qproData.hand.find(item => item.id === code.hand)
    if (!isRendingSet) nameObj.value.hand = targetQproData!.name
    // hand_r
    targetSpriteData = spriteData.find(item => item.type === "hand_r")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hand_l
    targetSpriteData = spriteData.find(item => item.type === "hand_l")
    targetSpriteData!.image = targetQproData!.webp_base64;
  }

  if (type === "face") {
    // face
    targetQproData = qproData.face.find(item => item.id === code.face)
    // face
    targetSpriteData = spriteData.find(item => item.type === "face")
    targetSpriteData!.image = targetQproData!.webp_base64;
  } else if (type === "hair") {
    // hair
    targetQproData = qproData.hair.find(item => item.id === code.hair)
    // hair_f
    targetSpriteData = spriteData.find(item => item.type === "hair_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hair_b
    targetSpriteData = spriteData.find(item => item.type === "hair_b")
    targetSpriteData!.image = targetQproData!.webp_base64;
  } else if (type === "head") {
    // head
    targetQproData = qproData.head.find(item => item.id === code.head)
    // head_f
    targetSpriteData = spriteData.find(item => item.type === "head_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // head_b
    targetSpriteData = spriteData.find(item => item.type === "head_b")
    targetSpriteData!.image = targetQproData!.webp_base64;
  } else if (type === "body") {
    // body
    targetQproData = qproData.body.find(item => item.id === code.body)
    // body_f
    targetSpriteData = spriteData.find(item => item.type === "body_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // body_b
    targetSpriteData = spriteData.find(item => item.type === "body_b")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // arm_r_upper
    targetSpriteData = spriteData.find(item => item.type === "arm_r_upper")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // arm_r_lower
    targetSpriteData = spriteData.find(item => item.type === "arm_r_lower")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // arm_l_upper
    targetSpriteData = spriteData.find(item => item.type === "arm_l_upper")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // arm_l_lower
    targetSpriteData = spriteData.find(item => item.type === "arm_l_lower")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // leg_r_upper
    targetSpriteData = spriteData.find(item => item.type === "leg_r_upper")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // leg_r_lower
    targetSpriteData = spriteData.find(item => item.type === "leg_r_lower")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // leg_l_upper
    targetSpriteData = spriteData.find(item => item.type === "leg_l_upper")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // leg_l_lower
    targetSpriteData = spriteData.find(item => item.type === "leg_l_lower")
    targetSpriteData!.image = targetQproData!.webp_base64;
  } else if (type === "hand") {
    // hand
    targetQproData = qproData.hand.find(item => item.id === code.hand)
    // hand_r
    targetSpriteData = spriteData.find(item => item.type === "hand_r")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hand_l
    targetSpriteData = spriteData.find(item => item.type === "hand_l")
    targetSpriteData!.image = targetQproData!.webp_base64;
  }


  for (const sprite of spriteData) {
    const img = await loadImage(`data:image/webp;base64,${sprite.image}`)

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
  if (chooseSet) {
    codeObj = { ...setsCode[code] }
  } else {
    codeObj[previewType] = code;
  }

  localStorage.setItem('qproCode', JSON.stringify(codeObj))

  await renderSprites(qproCanvas.value, codeObj)
}

function extractCompleteQproCodes(): qproCode[] {
  const suffixes: Record<QproKey, string> = {
    face: '_face',
    hair: '_hair',
    head: '_head',
    body: '_body',
    hand: '_hand'
  };

  const map: Map<string, qproCode> = new Map();

  (Object.keys(qproData) as QproKey[]).forEach((part) => {
    qproData[part].forEach(({ id, file_name }) => {
      const key = file_name
        .replace(/^qp_/, '')
        .replace(suffixes[part], '');

      if (!map.has(key)) {
        map.set(key, {
          face: -1,
          hair: -1,
          head: -1,
          body: -1,
          hand: -1
        });
      }

      map.get(key)![part] = id;
    });
  });

  const result: qproCode[] = [];
  for (const code of map.values()) {
    if (!Object.values(code).includes(-1)) {
      result.push(code);
    }
  }

  return result;
}

async function handelSetsClick() {
  chooseSet = true
  renderProgressPercentage.value = 0;
  const totalRendingQuery = setsCode.length
  let RendedQuery = 0
  qproPreviewList.value.length = 0;
  rending.value = true
  let previewCodeObj: qproCode;

  for (let i = 0; i < totalRendingQuery; i++) {
    previewCodeObj = setsCode[i]
    await renderSprites(tempCanvas, previewCodeObj, undefined, true);
    await new Promise<void>((resolve) => {
      tempCanvas.toBlob((blob) => {
        if (blob != null) {
          qproPreviewList.value.push(URL.createObjectURL(blob));
        }
        resolve();
      }, 'image/webp');
    });
    RendedQuery++
    renderProgressPercentage.value = Math.trunc(RendedQuery / totalRendingQuery * 100)
  }
  rending.value = false
}
</script>

<template>
  <el-dialog v-model="rending" title="Rending Qpro..." width="500" :show-close=false :close-on-click-modal=false :close-on-press-escape=false>
    <el-progress :percentage="renderProgressPercentage" />
    <span></span>
  </el-dialog>


  <div
    class="rounded-sm border-1 border-solid my-8 w-fit mx-auto shadow-xl border-gray-300 bg-white p-4 min-h-[calc(100vh-4rem)] flex flex-col md:flex-row items-center justify-center">
    <div class="flex flex-col items-center justify-center">
      <p class="text-2xl font-bold">QPro Previewer for IIDX 31</p>
      <canvas ref="qproCanvas" width="384" height="400" class="w-[70vw] sm:w-[25vw] h-auto"></canvas>
      <div class=" space-y-2 flex flex-col items-center justify-center">
        <div class="flex items-center justify-center">
          <el-button round :disabled="rending" @click="handelClick('face')" class=" w-16">Face</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.face }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('face')">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round :disabled="rending" @click="handelClick('hair')" class=" w-16">Hair</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.hair }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('hair')">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round :disabled="rending" @click="handelClick('head')" class=" w-16">Head</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.head }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('head')">Code</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round :disabled="rending" @click="handelClick('body')" class=" w-16">Body</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.body }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('body')">Code</el-button>
        </div>
        <div class="flex items-center justify-center mb-2">
          <el-button round :disabled="rending" @click="handelClick('hand')" class=" w-16">Hand</el-button>
          <p class=" font-black mx-2 text-center w-32">{{ nameObj.hand }}</p>
          <el-button type="primary" :icon="CopyDocument" @click="copyCode('hand')">Code</el-button>
        </div>
        <div class="flex items-center justify-center mb-2">
          <el-button round :disabled="rending" @click="handelSetsClick()" class=" w-16 mr-10">Sets</el-button>
          <el-button type="primary" :icon="Download" @click="saveImage" class="mb-1">Save Qpro As Image</el-button>
        </div>
        <p class="mb-1">Develop by DJ HITOMI with 🏳️‍⚧️</p>
      </div>
    </div>
    <div class="md:border-l-4 md:h-[80vh] md:mr-4 border-dotted border-gray-300"></div>
    <div class="flex flex-col items-center justify-center">
      <div class="md:border-none border-t-4 border-dotted border-gray-300 w-full"></div>
      <p class="text-2xl mb-2">QPro List</p>
      <div
        class="md:w-[calc(50vw+1rem)] md:h-[75vh] w-[70vw] h-[100vh] overflow-y-auto p-1 border-2 border-solid border-gray-300 rounded-sm">
        <div v-if="qproPreviewList" class="grid grid-cols-1 md:grid-cols-2 gap-2">
          <div v-for="(img, index) in qproPreviewList" :key="index" class="md:w-[384px] w-full h-auto relative border-4"
            :class="codeObj[previewType] === index && !chooseSet ? 'border-blue-500 rounded-sm' : 'border-transparent'"
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