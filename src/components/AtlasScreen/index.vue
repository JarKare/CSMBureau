<template>
  <div class="atlas-screen" :class="{ 'form-backdrop': !sayingShow }">
    <video v-if="mode === 'dark' && motionAllowed" ref="nightVideo" class="night-motion" autoplay muted loop playsinline
      aria-hidden="true" :poster="nightPoster" src="https://www.jianking.vip/resource/RainDate/night.mp4"></video>
    <div v-if="sayingShow" class="saying">
      <h1>{{ store.spring.feature }}</h1>
      <p v-if="store.spring.extract && store.spring.extract !== '无'">《{{ store.spring.extract }}》</p>
    </div>
    <slot></slot>
  </div>
</template>

<script setup>
import { inject, onActivated, onBeforeMount, onDeactivated, useTemplateRef } from 'vue'
import { useMediaQuery } from '@vueuse/core'
import nightPoster from '@/assets/images/night.jpg'

const store = inject('$store')
const request = inject('$request')
const mode = inject('themeMode')
const motionAllowed = useMediaQuery('(min-width: 769px) and (prefers-reduced-motion: no-preference)')
const props = defineProps({ sayingShow: { type: Boolean, default: true } })
const nightVideo = useTemplateRef('nightVideo')

onActivated(async () => {
  const video = nightVideo.value
  if (!video?.paused) return

  try {
    await video.play()
  } catch (err) {
    // Rapid navigation may interrupt a pending play request.
    if (err.name !== 'AbortError' && err.name !== 'NotAllowedError') console.warn(err)
  }
})

onDeactivated(() => nightVideo.value?.pause())

onBeforeMount(async () => {
  if (!props.sayingShow || store.spring.id !== 0) return
  try {
    const { code, data } = await request({ url: '/api/common/getAim', method: 'GET' })
    if (code === 0) store.setSpring(data)
  } catch (err) {
    console.error(err)
  }
})
</script>

<style scoped>
.atlas-screen { position: relative; isolation: isolate; display: grid; place-items: center; min-height: clamp(320px, 40vw, 536px); padding: 32px 20px; box-sizing: border-box; background: #efe6ce url('@/assets/images/mountains.jpg') center / cover; }
html.dark .atlas-screen { background: #111c32 url('@/assets/images/night.jpg') center 48% / cover; }
.night-motion, .atlas-screen::before { position: absolute; inset: 0; width: 100%; height: 100%; z-index: -2; object-fit: cover; }
html.dark .atlas-screen::before { content: ''; z-index: -1; background: rgba(8,18,40,.46); }
.saying { text-align: center; color: var(--site-ink); margin-top: -30px; }
.saying h1 { max-width: 16em; text-wrap: balance; font: 700 clamp(24px, 3.1vw, 44px)/1.65 var(--site-display); letter-spacing: .055em; }
.saying p { display: flex; align-items: center; justify-content: center; gap: 14px; margin-top: 14px; font: 500 16px var(--site-display); }
.saying p::before, .saying p::after { content: ''; width: 32px; height: 1px; background: currentColor; opacity: .55; }
html.dark .saying p { color: var(--site-accent); }
.form-backdrop { min-height: max(680px, calc(100svh - 64px)); }
</style>
