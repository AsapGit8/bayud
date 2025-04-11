<template>
  <nav class="navbar">
    <div class="navbar-container">
      <div class="navbar-brand">
        <NuxtLink to="/">Bayud</NuxtLink>
      </div>
      
      <div class="navbar-right">
        <NuxtLink to="/book" class="book-button">
          <span>Book</span>
        </NuxtLink>
        <button class="menu-toggle" @click="toggleMenu" aria-label="Toggle menu">
          <img 
            :src="menuOpen ? '/icons/CloseIcon.svg' : '/icons/NavbarIcon.svg'" 
            alt="Menu Icon"
            :class="{ 'rotate-icon': menuOpen }"
          />
        </button>
      </div>
    </div>

    <div ref="overlay" class="overlay-menu" :style="{ visibility: overlayVisible ? 'visible' : 'hidden' }">
      <div class="overlay-left"></div>
      <div class="overlay-content">
        <div class="menu-links">
          <NuxtLink to="/book" class="overlay-item" @click="closeMenu">
            <span>Book</span>
            <span class="underline"></span>
          </NuxtLink>
          <NuxtLink to="/activities" class="overlay-item" @click="closeMenu">
            <span>Activities</span>
            <span class="underline"></span>
          </NuxtLink>
          <NuxtLink to="/accommodations" class="overlay-item" @click="closeMenu">
            <span>Accommodations</span>
            <span class="underline"></span>
          </NuxtLink>
        </div>
        
        <div class="contact-info">
          <div class="contact-title">Contact us</div>
          <a href="mailto:bayudboutiqueresort@gmail.com" class="contact-email">
            bayudboutiqueresort@gmail.com
            <span class="underline"></span>
          </a>
        </div>
        
        <div class="credits">
          Site created by 
          <a href="https://morrowlab.studio" target="_blank" rel="noopener">
            Morrowlab Studio
            <span class="underline"></span>
          </a>
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from 'vue'
import gsap from 'gsap'

const menuOpen = ref(false)
const overlayVisible = ref(false)
const overlay = ref(null)
const isAnimating = ref(false)

const toggleMenu = () => {
  if (isAnimating.value) return
  menuOpen.value = !menuOpen.value
}

const closeMenu = () => {
  if (isAnimating.value) return
  menuOpen.value = false
}

const disableScroll = () => {
  document.body.style.overflow = 'hidden'
  document.documentElement.style.overflow = 'hidden'
}

const enableScroll = () => {
  document.body.style.overflow = ''
  document.documentElement.style.overflow = ''
}

const handleEscKey = (e) => {
  if (e.key === 'Escape' && menuOpen.value) {
    closeMenu()
  }
}

onMounted(() => {
  gsap.set(overlay.value, { 
    y: '-100%'
  })
  
  // Create hover animations for menu items
  const menuItems = document.querySelectorAll('.overlay-item, .contact-email, .credits a')
  
  menuItems.forEach(item => {
    const underline = item.querySelector('.underline')
    
    // Set initial state with consistent thickness and color
    gsap.set(underline, {
      width: 0,
      height: '1px',
      backgroundColor: 'rgba(255, 255, 255, 0.7)',
      transformOrigin: 'left center',
      immediateRender: true
    })
    
    // Create hover animations with proper cleanup
    let hoverAnimation = null
    
    item.addEventListener('mouseenter', () => {
      if (hoverAnimation) hoverAnimation.kill()
      
      hoverAnimation = gsap.to(underline, {
        width: '100%',
        duration: 0.6,
        ease: 'power2.out',
        overwrite: 'auto'
      })
    })
    
    item.addEventListener('mouseleave', () => {
      if (hoverAnimation) hoverAnimation.kill()
      
      gsap.to(underline, {
        width: 0,
        duration: 0.4,
        ease: 'power2.in',
        overwrite: 'auto'
      })
    })
  })
  
  window.addEventListener('keydown', handleEscKey)
})

onBeforeUnmount(() => {
  window.removeEventListener('keydown', handleEscKey)
  enableScroll()
})

watch(menuOpen, (isOpen) => {
  isAnimating.value = true
  
  if (isOpen) {
    overlayVisible.value = true
    disableScroll()
    
    gsap.to(overlay.value, {
      y: '0%',
      duration: 1.4,
      ease: 'sine.inOut',
      onComplete: () => {
        isAnimating.value = false
      }
    })
  } else {
    gsap.to(overlay.value, {
      y: '-100%',
      duration: 1.2,
      ease: 'sine.inOut',
      onComplete: () => {
        overlayVisible.value = false
        isAnimating.value = false
        enableScroll()
      }
    })
  }
})
</script>

<style scoped>
/* Navbar styles */
.navbar {
  position: fixed;
  top: 0;
  width: 100%;
  z-index: 1000;
  background: transparent;
}

.navbar-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem 2rem;
  max-width: 1440px;
  margin: 0 auto;
}

.navbar-brand a {
  font-size: 1.5rem;
  font-weight: bold;
  color: #333;
  text-decoration: none;
}

.navbar-right {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.book-button {
  background-color: #171716;
  color: white;
  padding: 0.5rem 1.25rem;
  border-radius: 4px;
  text-decoration: none;
  font-weight: 500;
  transition: background-color 0.2s ease;
}

.book-button:hover {
  background-color: #333;
}

.menu-toggle {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.5rem;
  z-index: 1001;
  transition: transform 0.3s ease;
}

.menu-toggle img {
  width: 24px;
  height: 24px;
  transition: transform 0.3s ease;
}

.menu-toggle:hover img:not(.rotate-icon) {
  transform: rotate(45deg);
}

.rotate-icon {
  transform: rotate(90deg);
}

/* Overlay styles */
.overlay-menu {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: #171716;
  display: flex;
  z-index: 999;
}

.overlay-left {
  width: 40%;
  background-color: #222;
}

.overlay-content {
  width: 60%;
  padding: 4rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.menu-links {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 1.5rem;
}

.overlay-item {
  color: white;
  font-size: 2.5rem;
  font-weight: 500;
  text-decoration: none;
  display: inline-block;
  position: relative;
  padding-bottom: 5px;
}

.underline {
  position: absolute;
  bottom: 0;
  left: 0;
  height: 1px !important;
  width: 0;
  display: block;
  will-change: width;
  background-color: rgba(255, 255, 255, 0.7) !important;
}

.contact-info {
  margin-top: auto;
  color: white;
}

.contact-title {
  font-size: 1.25rem;
  margin-bottom: 0.5rem;
  color: rgba(255, 255, 255, 0.9);
}

.contact-email {
  color: rgba(170, 170, 170, 0.9);
  text-decoration: none;
  font-size: 1rem;
  position: relative;
  display: inline-block;
  padding-bottom: 2px;
  transition: color 0.2s ease;
}

.contact-email:hover {
  color: rgba(200, 200, 200, 0.9);
}

.contact-email .underline {
  background-color: rgba(170, 170, 170, 0.7) !important;
}

.credits {
  color: rgba(102, 102, 102, 0.9);
  font-size: 0.875rem;
  margin-top: 2rem;
}

.credits a {
  color: rgba(136, 136, 136, 0.9);
  text-decoration: none;
  position: relative;
  display: inline-block;
  padding-bottom: 1px;
  transition: color 0.2s ease;
}

.credits a:hover {
  color: rgba(170, 170, 170, 0.9);
}

.credits a .underline {
  background-color: rgba(136, 136, 136, 0.7) !important;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .navbar-container {
    padding: 1rem;
  }
  
  .overlay-left {
    display: none;
  }
  
  .overlay-content {
    width: 100%;
    padding: 2rem;
  }
  
  .overlay-item {
    font-size: 2rem;
  }
  
  .contact-title {
    font-size: 1.1rem;
  }
  
  .contact-email {
    font-size: 0.9rem;
  }
  
  .credits {
    font-size: 0.8rem;
  }
}
</style>
