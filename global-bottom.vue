<template>
  <div class="hero" aria-hidden="true">
    <svg ref="svgElement" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid slice" class="hero-bg">
      <defs>
        <radialGradient id="Gradient1" cx="50%" cy="50%" fx="0.441602%" fy="50%" r=".5">
          <animate ref="anim1" attributeName="fx" begin="indefinite" dur="34s" values="0%;3%;0%" repeatCount="indefinite"></animate>
          <stop offset="0%" stop-color="rgba(210, 203, 255, 1)"></stop>
          <stop offset="100%" stop-color="rgba(51, 63, 124, 0)"></stop>
        </radialGradient>
        <radialGradient id="Gradient2" cx="50%" cy="50%" fx="2.68147%" fy="50%" r=".5">
          <animate ref="anim2" attributeName="fx" begin="indefinite" dur="23.5s" values="0%;3%;0%" repeatCount="indefinite"></animate>
          <stop offset="0%" stop-color="rgba(133, 109, 255, 1)"></stop>
          <stop offset="100%" stop-color="rgba(51, 63, 124, 0)"></stop>
        </radialGradient>
        <radialGradient id="Gradient3" cx="50%" cy="50%" fx="0.836536%" fy="50%" r=".5">
          <animate ref="anim3" attributeName="fx" begin="indefinite" dur="21.5s" values="0%;3%;0%" repeatCount="indefinite"></animate>
          <stop offset="0%" stop-color="rgba(210, 203, 255, 1)"></stop>
          <stop offset="100%" stop-color="rgba(51, 63, 124, 0)"></stop>
        </radialGradient>
      </defs>
      <rect x="13.744%" y="1.18473%" width="100%" height="100%" fill="url(#Gradient1)" transform="rotate(334.41 50 50)">
        <animate ref="rectAnim1x" attributeName="x" begin="indefinite" dur="20s" values="25%;0%;25%" repeatCount="indefinite"></animate>
        <animate ref="rectAnim1y" attributeName="y" begin="indefinite" dur="21s" values="0%;25%;0%" repeatCount="indefinite"></animate>
        <animateTransform ref="rectAnim1rot" attributeName="transform" type="rotate" begin="indefinite" from="0 50 50" to="360 50 50" dur="7s" repeatCount="indefinite"></animateTransform>
      </rect>
      <rect x="-2.17916%" y="35.4267%" width="100%" height="100%" fill="url(#Gradient2)" transform="rotate(255.072 50 50)">
        <animate ref="rectAnim2x" attributeName="x" begin="indefinite" dur="23s" values="-25%;0%;-25%" repeatCount="indefinite"></animate>
        <animate ref="rectAnim2y" attributeName="y" begin="indefinite" dur="24s" values="0%;50%;0%" repeatCount="indefinite"></animate>
        <animateTransform ref="rectAnim2rot" attributeName="transform" type="rotate" begin="indefinite" from="0 50 50" to="360 50 50" dur="12s" repeatCount="indefinite"></animateTransform>
      </rect>
      <rect x="9.00483%" y="14.5733%" width="100%" height="100%" fill="url(#Gradient3)" transform="rotate(139.903 50 50)">
        <animate ref="rectAnim3x" attributeName="x" begin="indefinite" dur="25s" values="0%;25%;0%" repeatCount="indefinite"></animate>
        <animate ref="rectAnim3y" attributeName="y" begin="indefinite" dur="12s" values="0%;25%;0%" repeatCount="indefinite"></animate>
        <animateTransform ref="rectAnim3rot" attributeName="transform" type="rotate" begin="indefinite" from="360 50 50" to="0 50 50" dur="9s" repeatCount="indefinite"></animateTransform>
      </rect>
    </svg>

    <svg aria-hidden="true" class="hero-pattern">
      <defs>
        <pattern id="hero-pattern" width="32" height="64" patternUnits="userSpaceOnUse" x="0" y="0">
          <path d="M0,28 L20,28 L20,16 L16,16 L16,24 L4,24 L4,4 L32,4 L32,32 L28,32 L28,8 L8,8 L8,20 L12,20 L12,12 L24,12 L24,32 L0,32 L0,28 Z M12,36 L32,36 L32,40 L16,40 L16,64 L0,64 L0,60 L12,60 L12,36 Z M28,48 L24,48 L24,60 L32,60 L32,64 L20,64 L20,44 L32,44 L32,56 L28,56 L28,48 Z M0,36 L8,36 L8,56 L0,56 L0,52 L4,52 L4,40 L0,40 L0,36 Z"
            fill="none"
            stroke-dasharray="0"
            stroke-width="1.4"
            :stroke="heroPatternStroke"
          ></path>
        </pattern>
      </defs>
      <rect width="100%" height="100%" stroke-width="0" fill="url(#hero-pattern)"></rect>
    </svg>
  </div>
</template>

<script setup>
import { computed, onMounted, onUnmounted, ref, watch } from 'vue'
import { useNav } from '@slidev/client'

const { currentSlideRoute } = useNav()

const svgElement = ref(null)
const anim1 = ref(null)
const anim2 = ref(null)
const anim3 = ref(null)
const rectAnim1x = ref(null)
const rectAnim1y = ref(null)
const rectAnim1rot = ref(null)
const rectAnim2x = ref(null)
const rectAnim2y = ref(null)
const rectAnim2rot = ref(null)
const rectAnim3x = ref(null)
const rectAnim3y = ref(null)
const rectAnim3rot = ref(null)

const animElements = [
  anim1, anim2, anim3, // Gradient anims
  rectAnim1x, rectAnim1y, rectAnim1rot, // Rect1 anims
  rectAnim2x, rectAnim2y, rectAnim2rot, // Rect2 anims
  rectAnim3x, rectAnim3y, rectAnim3rot  // Rect3 anims
]

const isDark = ref(false)

const heroPatternStroke = computed(() =>
  isDark.value ? 'rgba(255, 255, 255, 0.24)' : 'rgba(129, 140, 248, 0.38)'
)

let pauseTimer = null
let animationsStarted = false
let mutationObserver = null

const updateDarkMode = () => {
  isDark.value = document.documentElement.classList.contains('dark')
}

onMounted(() => {
  updateDarkMode()

  if (svgElement.value) {
    startAnimations()
    pauseAnimations()
  }

  watch(
    () => currentSlideRoute.value?.no,
    (newSlide, oldSlide) => {
      if (newSlide === oldSlide) return
      if (!animationsStarted) {
        startAnimations()
      }
      resumeAnimations()
      if (pauseTimer) {
        clearTimeout(pauseTimer)
      }
      pauseTimer = setTimeout(() => {
        pauseAnimations()
        pauseTimer = null
      }, 1500)
    },
    { immediate: false }
  )

  mutationObserver = new MutationObserver(updateDarkMode)
  mutationObserver.observe(document.documentElement, {
    attributes: true,
    attributeFilter: ['class'],
  })
})

onUnmounted(() => {
  if (mutationObserver) {
    mutationObserver.disconnect()
    mutationObserver = null
  }
  if (pauseTimer) {
    clearTimeout(pauseTimer)
    pauseTimer = null
  }
})

function startAnimations() {
  animElements.forEach((anim, index) => {
    if (anim.value && typeof anim.value.beginElement === 'function') {
      anim.value.beginElement()
    }
  })
  animationsStarted = true
}

function resumeAnimations() {
  if (svgElement.value && typeof svgElement.value.unpauseAnimations === 'function') {
    svgElement.value.unpauseAnimations()
  }
}

function pauseAnimations() {
  if (svgElement.value && typeof svgElement.value.pauseAnimations === 'function') {
    svgElement.value.pauseAnimations()
  }
}
</script>

<style scoped>
.hero {
  position: absolute;
  inset: 0;
  z-index: -10;
  overflow: hidden;
  pointer-events: none;
  isolation: isolate;
}

.hero .hero-bg {
  pointer-events: none;
  top: 0;
  z-index: -10;
  object-fit: cover;
  width: 100%;
  height: 100%;
  opacity: 0.35;
  transition: opacity 0.5s ease-in-out;
  position: absolute;
}

.hero-bg,
.hero-pattern {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.hero-pattern {
  pointer-events: none;
  top: 0;
  z-index: -10;
  width: 100%;
  height: 100%;
  stroke: rgba(59, 130, 246, 0.24);
  mask-image: linear-gradient(#ffffffad, transparent);
  -webkit-mask-image: linear-gradient(#ffffffad, transparent);
  opacity: 0.28;
}

.dark .hero-pattern {
  stroke: rgba(255, 255, 255, 0.24);
}
</style>