<script setup lang="ts">
import { nextTick, onMounted, ref, shallowRef, watch, type Ref, type ShallowRef } from 'vue'

import type { qproCode } from './types/qproCode'
import type { spriteItem } from './types/spriteItem'
import type { qproName } from './types/qproName'

import { CopyDocument, SuccessFilled } from '@element-plus/icons-vue'
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

let chooseSet: boolean = false

const faceDivRef = ref<HTMLElement | null>(null)
const faceRef = ref<HTMLElement | null>(null)
const hairDivRef = ref<HTMLElement | null>(null)
const hairRef = ref<HTMLElement | null>(null)
const headDivRef = ref<HTMLElement | null>(null)
const headRef = ref<HTMLElement | null>(null)
const bodyDivRef = ref<HTMLElement | null>(null)
const bodyRef = ref<HTMLElement | null>(null)
const handDivRef = ref<HTMLElement | null>(null)
const handRef = ref<HTMLElement | null>(null)

const loadQproData = ref<boolean>(false)

const renderProgressPercentage = ref<number>(0)

let qproData: QproDataL1 = {
  head: [],
  hair: [],
  face: [],
  hand: [],
  body: []
};

type QproKey = "head" | "hair" | "face" | "hand" | "body";

const normalSpriteLayer: string[] = [
  "hair_b",
  "head_b",
  "body_b",
  "arm_r_upper",
  "arm_r_lower",
  "leg_l_lower",
  "hand_r",
  "leg_r_lower",
  "leg_r_upper",
  "leg_l_upper",
  "body_f",
  "arm_l_upper",
  "arm_l_lower",
  "face",
  "hair_f",
  "hand_l",
  "head_f"
]

const qp_su30_fan_SpriteLayer: string[] = [
  "hair_b",
  "qp_su30_fan_head_b",
  "body_b",
  "arm_r_upper",
  "arm_r_lower",
  "leg_l_lower",
  "hand_r",
  "leg_r_lower",
  "leg_r_upper",
  "leg_l_upper",
  "body_f",
  "arm_l_upper",
  "arm_l_lower",
  "face",
  "hair_f",
  "hand_l",
  "qp_su30_fan_head_f1",
  "qp_su30_fan_head_f2",
]

const qp_su31_variety_SpriteLayer: string[] = [
  "hair_b",
  "head_b",
  "body_b",
  "arm_r_upper",
  "arm_r_lower",
  "leg_l_lower",
  "hand_r",
  "leg_r_lower",
  "leg_r_upper",
  "leg_l_upper",
  "body_f",
  "arm_l_upper",
  "arm_l_lower",
  "qp_su31_variety_face",
  "hair_f",
  "hand_l",
  "head_f"
]

const specialSpriteLayer: string[] = [
  "hair_b",
  "head_b",
  "body_b_special",
  "arm_r_upper_special",
  "arm_r_lower_special",
  "leg_l_lower_special",
  "hand_r",
  "leg_r_lower_special",
  "leg_r_upper_special",
  "leg_l_upper_special",
  "body_f",
  "arm_l_upper_special",
  "arm_l_lower_special",
  "face",
  "hair_f",
  "hand_l",
  "head_f"
]

const specialSpriteCode: number[] = [
  46,
  47,
  49,
  55,
  56,
  57,
  58,
  59,
  60,
  61,
  62,
  63,
  64,
  65,
  66,
  67,
  68,
  69,
  71,
  72,
  75,
  76,
  77,
  78,
  80,
  81,
  83,
  85
]

const qp_tricorosailor_SpriteLayer: string[] = [
  "hair_b",
  "head_b",
  "body_b",
  "arm_r_upper_special",
  "arm_r_lower_special",
  "leg_l_lower_special",
  "hand_r",
  "leg_r_lower_special",
  "leg_r_upper_special",
  "leg_l_upper_special",
  "body_f",
  "arm_l_upper_special",
  "arm_l_lower_special",
  "face",
  "hair_f",
  "hand_l",
  "head_f"
]


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
    // qp_su30_fan_head_b
    type: "qp_su30_fan_head_b",
    image: "",
    position: [524, 0],
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
    // body_b_special
    type: "body_b_special",
    image: "",
    position: [778, 132],
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
    // arm_r_upper_special
    type: "arm_r_upper_special",
    image: "",
    position: [260, 0],
    drawAt: [81, 181],
    size: [132, 194],
  },
  {
    // arm_r_lower_special
    type: "arm_r_lower_special",
    image: "",
    position: [260, 194],
    drawAt: [79, 184],
    size: [132, 194],
  },
  {
    // leg_l_lower_special
    type: "leg_l_lower_special",
    image: "",
    position: [676, 132],
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
    // leg_r_lower_special
    type: "leg_r_lower_special",
    image: "",
    position: [778, 0],
    drawAt: [125, 243],
    size: [102, 132],
  },
  {
    // leg_r_upper_special
    type: "leg_r_upper_special",
    image: "",
    position: [676, 0],
    drawAt: [125, 243],
    size: [102, 132],
  },
  {
    // leg_l_upper_special
    type: "leg_l_upper_special",
    image: "",
    position: [880, 0],
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
    // arm_l_upper_special
    type: "arm_l_upper_special",
    image: "",
    position: [392, 0],
    drawAt: [202, 181],
    size: [142, 194],
  },
  {
    // arm_l_lower_special
    type: "arm_l_lower_special",
    image: "",
    position: [534, 0],
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
    // qp_su31_variety_face
    type: "qp_su31_variety_face",
    image: "",
    position: [0, 0],
    drawAt: [89, 52],
    size: [254, 218],
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
  {
    // qp_su30_fan_head_f1
    type: "qp_su30_fan_head_f1",
    image: "",
    position: [262, 0],
    drawAt: [83, 25],
    size: [262, 352],
  },
  {
    // qp_su30_fan_head_f2
    type: "qp_su30_fan_head_f2",
    image: "",
    position: [0, 0],
    drawAt: [92, 96],
    size: [262, 352],
  }
]

const renderSpriteData: spriteItem[] = [
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
    // qp_su30_fan_head_b
    type: "qp_su30_fan_head_b",
    image: "",
    position: [524, 0],
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
    // body_b_special
    type: "body_b_special",
    image: "",
    position: [778, 132],
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
    // arm_r_upper_special
    type: "arm_r_upper_special",
    image: "",
    position: [260, 0],
    drawAt: [81, 181],
    size: [132, 194],
  },
  {
    // arm_r_lower_special
    type: "arm_r_lower_special",
    image: "",
    position: [260, 194],
    drawAt: [79, 184],
    size: [132, 194],
  },
  {
    // leg_l_lower_special
    type: "leg_l_lower_special",
    image: "",
    position: [676, 132],
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
    // leg_r_lower_special
    type: "leg_r_lower_special",
    image: "",
    position: [778, 0],
    drawAt: [125, 243],
    size: [102, 132],
  },
  {
    // leg_r_upper_special
    type: "leg_r_upper_special",
    image: "",
    position: [676, 0],
    drawAt: [125, 243],
    size: [102, 132],
  },
  {
    // leg_l_upper_special
    type: "leg_l_upper_special",
    image: "",
    position: [880, 0],
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
    // arm_l_upper_special
    type: "arm_l_upper_special",
    image: "",
    position: [392, 0],
    drawAt: [202, 181],
    size: [142, 194],
  },
  {
    // arm_l_lower_special
    type: "arm_l_lower_special",
    image: "",
    position: [534, 0],
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
    // qp_su31_variety_face
    type: "qp_su31_variety_face",
    image: "",
    position: [0, 0],
    drawAt: [89, 52],
    size: [254, 218],
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
  {
    // qp_su30_fan_head_f1
    type: "qp_su30_fan_head_f1",
    image: "",
    position: [262, 0],
    drawAt: [83, 25],
    size: [262, 352],
  },
  {
    // qp_su30_fan_head_f2
    type: "qp_su30_fan_head_f2",
    image: "",
    position: [0, 0],
    drawAt: [92, 96],
    size: [262, 352],
  }
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

watch(
  () => nameObj.value.face,
  async () => {
    await nextTick()
    if (faceRef.value) {
      if (faceRef.value.offsetWidth - faceDivRef.value!.clientWidth > 0) {
        addMarquee(faceRef.value.offsetWidth - faceDivRef.value!.clientWidth, "face")
      } else {
        removeMarquee("face")
      }
    }
  }
);

watch(
  () => nameObj.value.hair,
  async () => {
    await nextTick()
    if (hairRef.value) {
      if (hairRef.value.offsetWidth - hairDivRef.value!.clientWidth > 0) {
        addMarquee(hairRef.value.offsetWidth - hairDivRef.value!.clientWidth, "hair")
      } else {
        removeMarquee("hair")
      }
    }
  }
);

watch(
  () => nameObj.value.head,
  async () => {
    await nextTick()
    if (headRef.value) {
      if (headRef.value.offsetWidth - headDivRef.value!.clientWidth > 0) {
        addMarquee(headRef.value.offsetWidth - headDivRef.value!.clientWidth, "head")
      } else {
        removeMarquee("head")
      }
    }
  }
);

watch(
  () => nameObj.value.body,
  async () => {
    await nextTick()
    if (bodyRef.value) {
      if (bodyRef.value.offsetWidth - bodyDivRef.value!.clientWidth > 0) {
        addMarquee(bodyRef.value.offsetWidth - bodyDivRef.value!.clientWidth, "body")
      } else {
        removeMarquee("body")
      }
    }
  }
);

watch(
  () => nameObj.value.hand,
  async () => {
    await nextTick()
    if (handRef.value) {
      if (handRef.value.offsetWidth - handDivRef.value!.clientWidth > 0) {
        addMarquee(handRef.value.offsetWidth - handDivRef.value!.clientWidth, "hand")
      } else {
        removeMarquee("hand")
      }
    }
  }
);

const copyButtonTypeMap: Record<QproKey, Ref<string>> = {
  head: ref<'primary' | 'success'>('primary'),
  hair: ref<'primary' | 'success'>('primary'),
  face: ref<'primary' | 'success'>('primary'),
  hand: ref<'primary' | 'success'>('primary'),
  body: ref<'primary' | 'success'>('primary')
}

const copyButtonIconMap: Record<QproKey, ShallowRef> = {
  head: shallowRef(CopyDocument),
  hair: shallowRef(CopyDocument),
  face: shallowRef(CopyDocument),
  hand: shallowRef(CopyDocument),
  body: shallowRef(CopyDocument)
}


const copyButtonTextMap: Record<QproKey, Ref<string>> = {
  head: ref('Code'),
  hair: ref('Code'),
  face: ref('Code'),
  hand: ref('Code'),
  body: ref('Code')
}

const qproPreviewList = ref<string[]>([])

let previewType: QproKey

const qproCanvas = ref<HTMLCanvasElement | null>(null)

let renderCanvas: HTMLCanvasElement = document.createElement('canvas');
renderCanvas.width = 384;
renderCanvas.height = 400;

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
    await renderSprites(renderCanvas, previewCodeObj, option);
    await new Promise<void>((resolve) => {
      renderCanvas.toBlob((blob) => {
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
  loadQproData.value = true;
  qproData = (await import('./assets/qpro31_webp.json', {
    assert: { type: 'json' }
  })).default as QproDataL1;
  loadQproData.value = false;

  setsCode = extractCompleteQproCodes()

  const saved = localStorage.getItem('qproCode')

  if (saved) {
    codeObj = JSON.parse(saved)
  }

  // qp_su30_fan_head
  let targetQproData: QproDataL2 | undefined
  let targetSpriteData: spriteItem | undefined
  targetQproData = qproData.head.find(item => item.id === 356)
  // qp_su30_fan_head_f1
  targetSpriteData = spriteData.find(item => item.type === "qp_su30_fan_head_f1")
  targetSpriteData!.image = targetQproData!.webp_base64;
  // qp_su30_fan_head_f2
  targetSpriteData = spriteData.find(item => item.type === "qp_su30_fan_head_f2")
  targetSpriteData!.image = targetQproData!.webp_base64;
  // qp_su30_fan_head_b
  targetSpriteData = spriteData.find(item => item.type === "qp_su30_fan_head_b")
  targetSpriteData!.image = targetQproData!.webp_base64;

  // qp_su30_fan_head_f1
  targetSpriteData = renderSpriteData.find(item => item.type === "qp_su30_fan_head_f1")
  targetSpriteData!.image = targetQproData!.webp_base64;
  // qp_su30_fan_head_f2
  targetSpriteData = renderSpriteData.find(item => item.type === "qp_su30_fan_head_f2")
  targetSpriteData!.image = targetQproData!.webp_base64;
  // qp_su30_fan_head_b
  targetSpriteData = renderSpriteData.find(item => item.type === "qp_su30_fan_head_b")
  targetSpriteData!.image = targetQproData!.webp_base64;



  // qp_su31_variety_face
  targetQproData = qproData.face.find(item => item.id === 266)
  // qp_su31_variety_face
  targetSpriteData = spriteData.find(item => item.type === "qp_su31_variety_face")
  targetSpriteData!.image = targetQproData!.webp_base64;

  // qp_su31_variety_face
  targetSpriteData = renderSpriteData.find(item => item.type === "qp_su31_variety_face")
  targetSpriteData!.image = targetQproData!.webp_base64;

  await renderSprites(qproCanvas.value, codeObj)
})

async function renderSprites(
  canvas: HTMLCanvasElement | null,
  code: qproCode,
  type?: QproKey,
  isRendingSet?: boolean
): Promise<void> {
  if (!canvas) return

  const ctx = canvas.getContext('2d')
  if (!ctx) return

  ctx.clearRect(0, 0, canvas.width, canvas.height);

  let targetQproData
  let targetSpriteData

  let sprites

  if (type === undefined && isRendingSet === undefined) {
    // face
    targetQproData = qproData.face.find(item => item.id === code.face)
    nameObj.value.face = targetQproData!.name;
    // face
    targetSpriteData = spriteData.find(item => item.type === "face")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // hair
    targetQproData = qproData.hair.find(item => item.id === code.hair)
    nameObj.value.hair = targetQproData!.name;
    // hair_f
    targetSpriteData = spriteData.find(item => item.type === "hair_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hair_b
    targetSpriteData = spriteData.find(item => item.type === "hair_b")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // head
    targetQproData = qproData.head.find(item => item.id === code.head)
    nameObj.value.head = targetQproData!.name;
    // head_f
    targetSpriteData = spriteData.find(item => item.type === "head_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // head_b
    targetSpriteData = spriteData.find(item => item.type === "head_b")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // body
    targetQproData = qproData.body.find(item => item.id === code.body)
    nameObj.value.body = targetQproData!.name;
    // body_f
    targetSpriteData = spriteData.find(item => item.type === "body_f")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // arm & leg
    if (specialSpriteCode.includes(code.body)) {
    // body_b
    targetSpriteData = spriteData.find(item => item.type === "body_b_special")
    targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_upper
      targetSpriteData = spriteData.find(item => item.type === "arm_r_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_lower
      targetSpriteData = spriteData.find(item => item.type === "arm_r_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_upper
      targetSpriteData = spriteData.find(item => item.type === "arm_l_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_lower
      targetSpriteData = spriteData.find(item => item.type === "arm_l_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_upper
      targetSpriteData = spriteData.find(item => item.type === "leg_r_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_lower
      targetSpriteData = spriteData.find(item => item.type === "leg_r_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_upper
      targetSpriteData = spriteData.find(item => item.type === "leg_l_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_lower
      targetSpriteData = spriteData.find(item => item.type === "leg_l_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
    } else {
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
    }

    // hand
    targetQproData = qproData.hand.find(item => item.id === code.hand)
    nameObj.value.hand = targetQproData!.name;
    // hand_r
    targetSpriteData = spriteData.find(item => item.type === "hand_r")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hand_l
    targetSpriteData = spriteData.find(item => item.type === "hand_l")
    targetSpriteData!.image = targetQproData!.webp_base64;

    sprites = spriteData
  } else if (isRendingSet) {
    // face
    targetQproData = qproData.face.find(item => item.id === code.face)
    // face
    targetSpriteData = renderSpriteData.find(item => item.type === "face")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // hair
    targetQproData = qproData.hair.find(item => item.id === code.hair)
    // hair_f
    targetSpriteData = renderSpriteData.find(item => item.type === "hair_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hair_b
    targetSpriteData = renderSpriteData.find(item => item.type === "hair_b")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // head
    targetQproData = qproData.head.find(item => item.id === code.head)
    // head_f
    targetSpriteData = renderSpriteData.find(item => item.type === "head_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // head_b
    targetSpriteData = renderSpriteData.find(item => item.type === "head_b")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // body
    targetQproData = qproData.body.find(item => item.id === code.body)
    // body_f
    targetSpriteData = renderSpriteData.find(item => item.type === "body_f")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // arm & leg
    if (specialSpriteCode.includes(code.body)) {
    // body_b
    targetSpriteData = renderSpriteData.find(item => item.type === "body_b_upper_special")
    targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
    } else {
    // body_b
    targetSpriteData = renderSpriteData.find(item => item.type === "body_b")
    targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_upper")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_lower")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_upper")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_lower")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_upper")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_lower")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_upper")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_lower")
      targetSpriteData!.image = targetQproData!.webp_base64;
    }

    // hand
    targetQproData = qproData.hand.find(item => item.id === code.hand)
    // hand_r
    targetSpriteData = renderSpriteData.find(item => item.type === "hand_r")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hand_l
    targetSpriteData = renderSpriteData.find(item => item.type === "hand_l")
    targetSpriteData!.image = targetQproData!.webp_base64;

    sprites = renderSpriteData
  } else if (code[type!] === 0) {
    // face
    targetQproData = qproData.face.find(item => item.id === code.face)
    if (!isRendingSet) nameObj.value.face = targetQproData!.name;
    // face
    targetSpriteData = renderSpriteData.find(item => item.type === "face")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // hair
    targetQproData = qproData.hair.find(item => item.id === code.hair)
    if (!isRendingSet) nameObj.value.hair = targetQproData!.name;
    // hair_f
    targetSpriteData = renderSpriteData.find(item => item.type === "hair_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hair_b
    targetSpriteData = renderSpriteData.find(item => item.type === "hair_b")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // head
    targetQproData = qproData.head.find(item => item.id === code.head)
    if (!isRendingSet) nameObj.value.head = targetQproData!.name;
    // head_f
    targetSpriteData = renderSpriteData.find(item => item.type === "head_f")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // head_b
    targetSpriteData = renderSpriteData.find(item => item.type === "head_b")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // body
    targetQproData = qproData.body.find(item => item.id === code.body)
    if (!isRendingSet) nameObj.value.body = targetQproData!.name;
    // body_f
    targetSpriteData = renderSpriteData.find(item => item.type === "body_f")
    targetSpriteData!.image = targetQproData!.webp_base64;

    // arm & leg
    if (specialSpriteCode.includes(code.body)) {
    // body_b
    targetSpriteData = renderSpriteData.find(item => item.type === "body_b_special")
    targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_upper_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_lower_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
    } else {
    // body_b
    targetSpriteData = renderSpriteData.find(item => item.type === "body_b")
    targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_upper")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_r_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_lower")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_upper")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // arm_l_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_lower")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_upper")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_r_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_lower")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_upper
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_upper")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // leg_l_lower
      targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_lower")
      targetSpriteData!.image = targetQproData!.webp_base64;
    }


    // hand
    targetQproData = qproData.hand.find(item => item.id === code.hand)
    if (!isRendingSet) nameObj.value.hand = targetQproData!.name;
    // hand_r
    targetSpriteData = renderSpriteData.find(item => item.type === "hand_r")
    targetSpriteData!.image = targetQproData!.webp_base64;
    // hand_l
    targetSpriteData = renderSpriteData.find(item => item.type === "hand_l")
    targetSpriteData!.image = targetQproData!.webp_base64;

    sprites = renderSpriteData
  } else {
    if (type === "face") {
      // face
      targetQproData = qproData.face.find(item => item.id === code.face)
      // face
      targetSpriteData = renderSpriteData.find(item => item.type === "face")
      targetSpriteData!.image = targetQproData!.webp_base64;
    } else if (type === "hair") {
      // hair
      targetQproData = qproData.hair.find(item => item.id === code.hair)
      // hair_f
      targetSpriteData = renderSpriteData.find(item => item.type === "hair_f")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // hair_b
      targetSpriteData = renderSpriteData.find(item => item.type === "hair_b")
      targetSpriteData!.image = targetQproData!.webp_base64;
    } else if (type === "head") {
      // head
      targetQproData = qproData.head.find(item => item.id === code.head)
      // head_f
      targetSpriteData = renderSpriteData.find(item => item.type === "head_f")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // head_b
      targetSpriteData = renderSpriteData.find(item => item.type === "head_b")
      targetSpriteData!.image = targetQproData!.webp_base64;
    } else if (type === "body") {
      // body
      targetQproData = qproData.body.find(item => item.id === code.body)
      // body_f
      targetSpriteData = renderSpriteData.find(item => item.type === "body_f")
      targetSpriteData!.image = targetQproData!.webp_base64;

      // arm & leg
      if (specialSpriteCode.includes(code.body)) {
      // body_b
      targetSpriteData = renderSpriteData.find(item => item.type === "body_b_special")
      targetSpriteData!.image = targetQproData!.webp_base64;
        // arm_r_upper
        targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_upper_special")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // arm_r_lower
        targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_lower_special")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // arm_l_upper
        targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_upper_special")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // arm_l_lower
        targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_lower_special")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // leg_r_upper
        targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_upper_special")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // leg_r_lower
        targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_lower_special")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // leg_l_upper
        targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_upper_special")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // leg_l_lower
        targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_lower_special")
        targetSpriteData!.image = targetQproData!.webp_base64;
      } else {
      // body_b
      targetSpriteData = renderSpriteData.find(item => item.type === "body_b")
      targetSpriteData!.image = targetQproData!.webp_base64;
        // arm_r_upper
        targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_upper")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // arm_r_lower
        targetSpriteData = renderSpriteData.find(item => item.type === "arm_r_lower")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // arm_l_upper
        targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_upper")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // arm_l_lower
        targetSpriteData = renderSpriteData.find(item => item.type === "arm_l_lower")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // leg_r_upper
        targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_upper")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // leg_r_lower
        targetSpriteData = renderSpriteData.find(item => item.type === "leg_r_lower")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // leg_l_upper
        targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_upper")
        targetSpriteData!.image = targetQproData!.webp_base64;
        // leg_l_lower
        targetSpriteData = renderSpriteData.find(item => item.type === "leg_l_lower")
        targetSpriteData!.image = targetQproData!.webp_base64;
      }
    } else if (type === "hand") {
      // hand
      targetQproData = qproData.hand.find(item => item.id === code.hand)
      // hand_r
      targetSpriteData = renderSpriteData.find(item => item.type === "hand_r")
      targetSpriteData!.image = targetQproData!.webp_base64;
      // hand_l
      targetSpriteData = renderSpriteData.find(item => item.type === "hand_l")
      targetSpriteData!.image = targetQproData!.webp_base64;
    }
    sprites = renderSpriteData
  }

  for (const sprite of sprites) {
    if ((code.head != 356 && code.head != 266 && !specialSpriteCode.includes(code.body) && normalSpriteLayer.includes(sprite.type)) ||
      (code.head === 356 && qp_su30_fan_SpriteLayer.includes(sprite.type)) ||
      (code.face === 266 && qp_su31_variety_SpriteLayer.includes(sprite.type)) ||
      (specialSpriteCode.includes(code.body) && specialSpriteLayer.includes(sprite.type))
    ) {
      const img = await loadImage(`data:image/webp;base64,${sprite.image}`)
      console.log(sprite.type)
      const [sx, sy] = sprite.position
      const [dx, dy] = sprite.drawAt
      const [dw, dh] = sprite.size
      ctx.drawImage(img, sx, sy, dw, dh, dx, dy, dw, dh)
    }
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

  copyButtonTypeMap[qproKey].value = 'success'
  copyButtonIconMap[qproKey].value = SuccessFilled
  copyButtonTextMap[qproKey].value = 'Copied!'

  setTimeout(() => {
    copyButtonTypeMap[qproKey].value = 'primary'
    copyButtonIconMap[qproKey].value = CopyDocument
    copyButtonTextMap[qproKey].value = 'Code'

  }, 3000)
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
  result.push({
    face: 0,
    hair: 0,
    head: 0,
    body: 0,
    hand: 0
  })
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
    await renderSprites(renderCanvas, previewCodeObj, undefined, true);
    await new Promise<void>((resolve) => {
      renderCanvas.toBlob((blob) => {
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

function removeMarquee(styleId: string) {
  styleId = styleId + "-marquee"
  const existingStyle = document.getElementById(styleId);
  if (existingStyle) {
    existingStyle.remove();
  }
}

function addMarquee(move: number, styleId: string) {
  styleId = styleId + "-marquee"
  const existingStyle = document.getElementById(styleId);
  if (existingStyle) {
    existingStyle.remove();
  }

  const style = document.createElement('style');
  style.id = styleId;

  style.textContent = `
      @keyframes ${styleId} {
      
      0%   { transform: translateX(${-move}px); }
      45%  { transform: translateX(0); }
      50%  { transform: translateX(0); }
      95%  { transform: translateX(${-move}px); }
      100% { transform: translateX(${-move}px); }
    }

    .${styleId} {
      animation: ${styleId} 6s ease-in-out infinite;
    }
  `;

  document.head.appendChild(style);
}
</script>

<template>
  <el-dialog v-model="loadQproData" width="500" class="max-w-[90vw]" :show-close=false :close-on-click-modal=false
    :close-on-press-escape=false>
    <template #header>
      <div class="flex items-center">
        <img class="mr-3 size-5 animate-spin" src="/fish-cake.svg" alt="loading" />
        Loading Qpro...
      </div>
    </template>
  </el-dialog>

  <div
    class="rounded-xl border-1 border-solid my-8 w-fit mx-auto shadow-xl border-gray-300 bg-white p-4 min-h-[calc(100vh-4rem)] flex flex-col md:flex-row items-center justify-center font-roboto">
    <div class="flex flex-col items-center justify-center">
      <p class="text-2xl font-extrabold">QPro Previewer for IIDX 31</p>
      <canvas ref="qproCanvas" width="384" height="400" class="w-[70vw] md:w-[25vw] h-auto"></canvas>
      <div class=" space-y-2 flex flex-col items-center justify-center">
        <div class="flex items-center justify-center">
          <el-button round :disabled="rending" @click="handelClick('face')" class=" w-16">Face</el-button>
          <div ref="faceDivRef" class="marquee-container w-32 mx-2 text-center overflow-hidden whitespace-nowrap">
            <p ref="faceRef" class="font-black inline-block face-marquee">{{ nameObj.face }}</p>
          </div>
          <el-button :type="copyButtonTypeMap['face'].value" :icon="copyButtonIconMap['face'].value"
            @click="copyCode('face')" class=" w-22">{{
              copyButtonTextMap['face'] }}</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round :disabled="rending" @click="handelClick('hair')" class=" w-16">Hair</el-button>
          <div ref="hairDivRef" class="marquee-container w-32 mx-2 text-center overflow-hidden whitespace-nowrap">
            <p ref="hairRef" class="font-black inline-block hair-marquee">{{ nameObj.hair }}</p>
          </div>
          <el-button :type="copyButtonTypeMap['hair'].value" :icon="copyButtonIconMap['hair'].value"
            @click="copyCode('hair')" class=" w-22">{{
              copyButtonTextMap['hair'] }}</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round :disabled="rending" @click="handelClick('head')" class=" w-16">Head</el-button>
          <div ref="headDivRef" class="marquee-container w-32 mx-2 text-center overflow-hidden whitespace-nowrap">
            <p ref="headRef" class="font-black inline-block head-marquee">{{ nameObj.head }}</p>
          </div>
          <el-button :type="copyButtonTypeMap['head'].value" :icon="copyButtonIconMap['head'].value"
            @click="copyCode('head')" class=" w-22">{{
              copyButtonTextMap['head'] }}</el-button>
        </div>
        <div class="flex items-center justify-center">
          <el-button round :disabled="rending" @click="handelClick('body')" class=" w-16">Body</el-button>
          <div ref="bodyDivRef" class="marquee-container w-32 mx-2 text-center overflow-hidden whitespace-nowrap">
            <p ref="bodyRef" class="font-black inline-block body-marquee">{{ nameObj.body }}</p>
          </div>
          <el-button :type="copyButtonTypeMap['body'].value" :icon="copyButtonIconMap['body'].value"
            @click="copyCode('body')" class=" w-22">{{
              copyButtonTextMap['body'] }}</el-button>
        </div>
        <div class="flex items-center justify-center mb-2">
          <el-button round :disabled="rending" @click="handelClick('hand')" class=" w-16">Hand</el-button>
          <div ref="handDivRef" class="marquee-container w-32 mx-2 text-center overflow-hidden whitespace-nowrap">
            <p ref="handRef" class="font-black inline-block hand-marquee">{{ nameObj.hand }}</p>
          </div>
          <el-button :type="copyButtonTypeMap['hand'].value" :icon="copyButtonIconMap['hand'].value"
            @click="copyCode('hand')" class=" w-22">{{
              copyButtonTextMap['hand'] }}</el-button>
        </div>
        <div class="flex items-center justify-center mb-2">
          <el-button round :disabled="rending" @click="handelSetsClick()" class=" w-16 mr-10">Sets</el-button>
          <el-button type="primary" :icon="Download" @click="saveImage" class="mb-1">Save Qpro As Image</el-button>
        </div>
        <p class="mb-1">Develop by DJ <span
            class="font-bold bg-gradient-to-r from-[#5BCEFA] to-[#F5A9B8] bg-clip-text text-transparent">HITOMI</span>
          with 🏳️‍⚧️</p>
      </div>
    </div>
    <div
      class=" md:ml-8 md:w-[calc(50vw+1rem)] md:min-h-[calc(100vh-8rem)] w-[70vw] h-[70vh] overflow-y-auto border border-gray-200 shadow-xl rounded-xl">
      <div class="border-b border-gray-200 sticky top-0 z-50 bg-white">
        <p class="text-2xl text-center">QPro List</p>
        <div class="grid grid-cols-[auto_1fr] pb" v-if="qproPreviewList.length != 0">
          <div class="size-5 mr-4">
            <img class="animate-spin" v-if="rending" src="/fish-cake.svg" alt="loading" />
          </div>
          <el-progress :percentage="renderProgressPercentage" :status="rending ? '' : 'success'" />
        </div>
      </div>
      <div v-if="qproPreviewList.length != 0" class="grid grid-cols-1 md:grid-cols-2 gap-2 px-2">
        <div v-for="(img, index) in qproPreviewList" :key="index"
          class="md:w-[calc((50vw-1rem)/2)] w-full h-auto relative rounded-xl border-4 hover:shadow-xl"
          :class="(codeObj[previewType] === index && !chooseSet) || (JSON.stringify(setsCode[index]) === JSON.stringify(codeObj) && chooseSet) ? 'border border-blue-500 rounded-md bg-blue-100' : 'border-transparent'"
          @click="codeObj[previewType] = index; changeSprite(index)">
          <img draggable="false" :src="img" alt="loading" class="object-cover" />
          <div
            v-if="(codeObj[previewType] === index && !chooseSet) || (JSON.stringify(setsCode[index]) === JSON.stringify(codeObj) && chooseSet)"
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
</template>

<style scoped>
.marquee-container {
  position: relative;
  white-space: nowrap;
  overflow: hidden;
}
</style>