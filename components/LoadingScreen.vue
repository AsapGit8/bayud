<template>
  <div v-if="!done" class="preloader" @click="skip">
    <div ref="frame" class="frame">
      <div v-show="slideshowActive" class="slideshow">
        <img :src="currentImage" alt="slideshow" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'nuxt/app'
import gsap from 'gsap'

const router = useRouter()
const done = ref(false)
const frame = ref(null)
const slideshowActive = ref(false)
const currentImage = ref('')
const images = Array.from({ length: 10 }, (_, i) => `/loading-images/img${i + 1}.jpg`)
let interval = null
let timeout = null

const skip = () => {
  if (done.value) return
  clearInterval(interval)
  clearTimeout(timeout)
  finishSequence()
}

onMounted(() => {
  const tl = gsap.timeline()
  
  // Check if mobile
  const isMobile = window.innerWidth < 768
  
  // Get the appropriate frame dimensions based on device
  const frameWidth = isMobile ? '80%' : '30%'
  const frameHeight = isMobile ? '50vh' : '70vh'
  
  // Initial collapsed horizontal line
  tl.set(frame.value, {
    width: 0,
    height: '4px',
    background: '#000',
    overflow: 'hidden',
  })
  
  // Expand width based on device
  tl.to(frame.value, {
    duration: 1,
    width: frameWidth,
    ease: 'expo.inOut',
  })
  
  // Expand height based on device
  tl.to(frame.value, {
    duration: 1,
    height: frameHeight,
    ease: 'expo.inOut',
    onComplete: () => {
      slideshowActive.value = true
      startSlideshow()
    },
  })
  
  // Auto-complete after 8s
  timeout = setTimeout(() => {
    skip()
  }, 8000)
})

const startSlideshow = () => {
  let index = 0
  currentImage.value = images[0]
  
  interval = setInterval(() => {
    index++
    
    if (index >= images.length) {
      // Once last image is shown
      clearInterval(interval)
      skip()
      return
    }
    
    currentImage.value = images[index]
  }, 400) // 0.4 seconds per image
}

const finishSequence = () => {
  const tl = gsap.timeline()
  
  // Collapse from top to bottom
  tl.to(frame.value, {
    y: '100%',
    duration: 1.2,
    ease: 'expo.inOut',
    onComplete: () => {
      done.value = true
      router.push('/')
    },
  })
}
</script>

<style scoped>
.preloader {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #d7d2ca;
  z-index: 9999;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.frame {
  background: #000;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  position: relative;
}

.slideshow {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

.slideshow img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  pointer-events: none;
}
</style>