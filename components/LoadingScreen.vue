<template>
  <div v-if="!done" class="preloader" @click="skip">
    <div ref="frame" class="frame">
      <div v-show="slideshowActive" class="slideshow">
        <img :src="currentImage" alt="slideshow" />
      </div>
      <p v-show="captionVisible" ref="caption" class="caption">Experience – Bayud Siargao</p>
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
const caption = ref(null)
const slideshowActive = ref(false)
const captionVisible = ref(false)
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
    ease: 'power2.inOut',
  })
  
  // Expand height based on device
  tl.to(frame.value, {
    duration: 1,
    height: frameHeight,
    ease: 'power2.inOut',
    onComplete: () => {
      slideshowActive.value = true
      captionVisible.value = true
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
      
      // Hold on last image for a second
      setTimeout(() => {
        if (!done.value) finishSequence()
      }, 1000)
      
      return
    }
    
    currentImage.value = images[index]
  }, 400) // 0.4 seconds per image
}

const finishSequence = () => {
  const tl = gsap.timeline()
  
  // Hide slideshow but keep frame
  tl.to(".slideshow", {
    duration: 0.4,
    opacity: 0,
    ease: "power2.inOut",
  })
  
  // Fade out caption
  tl.to(caption.value, {
    duration: 0.5,
    opacity: 0,
    ease: 'power2.inOut'
  }, "<")
  
  // Collapse height smoothly
  tl.to(frame.value, {
    duration: 1,
    height: '4px',
    ease: 'power2.inOut',
  }, "<=0.2")
  
  // Collapse width to center
  tl.to(frame.value, {
    duration: 1,
    width: 0,
    ease: 'power2.inOut',
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
  transition: opacity 0.4s ease;
}

.caption {
  color: white;
  font-family: sans-serif;
  font-size: 1.2rem;
  font-weight: 500;
  position: relative;
  z-index: 2;
  text-shadow: 0 0 10px rgba(0,0,0,0.5);
  mix-blend-mode: difference;
}
</style>