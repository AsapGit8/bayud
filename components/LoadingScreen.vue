<template>
  <div v-if="!done" class="preloader" @click="skip">
    <div ref="frame" class="frame">
      <div v-show="slideshowActive" class="slideshow">
        <!-- Main image container with clip-path -->
        <div class="image-container">
          <img :src="currentImage" alt="slideshow" />
        </div>
        
        <!-- Experience text overlay -->
        <div class="experience-text">
          <h2 class="title">experience</h2>
          <h1 class="location">Bayud Siargao</h1>
        </div>
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
  const frameHeight = isMobile ? '60vh' : '80vh'
  
  // Initial collapsed horizontal line
  tl.set(frame.value, {
    width: 0,
    height: '4px',
    background: '#2b3530', // Green background for the start
    overflow: 'hidden',
    transformOrigin: 'top center'
  })
  
  // Expand width based on device
  tl.to(frame.value, {
    duration: 1,
    width: frameWidth,
    ease: 'expo.inOut',
  })
  
  // Expand height based on device
  tl.to(frame.value, {
    duration: 1.6,
    height: frameHeight,
    ease: 'expo.inOut',
    onComplete: () => {
      slideshowActive.value = true
      startSlideshow()
      
      // Animate the text once slideshow is active
      animateText()
    },
  })
  
  // Auto-complete after 8s
  timeout = setTimeout(() => {
    skip()
  }, 8000)
})

const animateText = () => {
  const textElements = frame.value.querySelectorAll('.experience-text h1, .experience-text h2')
  
  gsap.fromTo(textElements, {
    y: 100,
    opacity: 0
  }, {
    y: 0,
    opacity: 1,
    stagger: 0.2,
    duration: 1.2,
    ease: 'power3.out'
  })
}

const startSlideshow = () => {
  let index = 0
  currentImage.value = images[0]
  
  // Initial reveal animation - make image fully visible
  const imageContainer = frame.value.querySelector('.image-container')
  gsap.to(imageContainer, {
    clipPath: 'inset(0% 0% 0% 0%)', // Fully visible
    duration: 1.5,
    ease: 'power3.inOut'
  })
  
  interval = setInterval(() => {
    index++
    
    if (index >= images.length) {
      // Once last image is shown
      clearInterval(interval)
      skip()
      return
    }
    
    // Just update the image source - no clip-path animation between images
    currentImage.value = images[index]
    
  }, 800) // 0.8 seconds per image
}

const finishSequence = () => {
  const tl = gsap.timeline()
  
  // Animate text out first
  tl.to(frame.value.querySelectorAll('.experience-text h1, .experience-text h2'), {
    y: -50,
    opacity: 0,
    stagger: 0.1,
    duration: 0.6,
    ease: 'power3.in'
  })
  
  // Animate the clip path to hide image from bottom to top
  const imageContainer = frame.value.querySelector('.image-container')
  tl.to(imageContainer, {
    clipPath: 'inset(100% 0% 0% 0%)', // Hide from bottom
    duration: 1.5,
    ease: 'expo.inOut',
  }, "-=0.3")
  
  // Then collapse the frame itself
  tl.to(frame.value, {
    height: 0,
    duration: 1.4,
    ease: 'expo.inOut',
    onComplete: () => {
      done.value = true
      router.push('/')
    },
  }, '-=0.8') // Overlap the animations slightly
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
  overflow: hidden;
}

.image-container {
  width: 100%;
  height: 100%;
  clip-path: inset(100% 0% 0% 0%); /* Initial state - not visible, hidden from top */
  position: relative;
}

.slideshow img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  pointer-events: none;
}

.experience-text {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 2rem;
  z-index: 3;
  pointer-events: none;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  color: white;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}

.experience-text .title {
  font-family: 'Manrope', sans-serif;
  font-weight: 300;
  font-size: 1.2rem;
  margin: 0;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.experience-text .location {
  font-family: 'Vercetti', serif;
  font-weight: 400;
  font-size: 3rem;
  margin: 0.2rem 0 0 0;
  line-height: 1;
}

@media (max-width: 768px) {
  .experience-text {
    padding: 1.5rem;
  }
  
  .experience-text .title {
    font-size: 1rem;
  }
  
  .experience-text .location {
    font-size: 2.2rem;
  }
}
</style>