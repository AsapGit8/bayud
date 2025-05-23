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
          <NuxtLink to="/experiences" class="overlay-item" @click="closeMenu">
            <span>Experiences</span>
            <span class="underline"></span>
          </NuxtLink>
          <NuxtLink to="/accommodations" class="overlay-item" @click="closeMenu">
            <span>Accommodations</span>
            <span class="underline"></span>
          </NuxtLink>
        </div>
        
        <div class="contact-section">
          <div class="social-links">
            <div class="social-icons">
              <a href="https://www.instagram.com/bayudsiargao/?hl=en" target="_blank" rel="noopener" class="social-icon">
                <img src="/icons/InstagramIcon.svg" alt="Instagram" />
              </a>
              <a href="https://www.facebook.com/bayudboutiqueresort/" target="_blank" rel="noopener" class="social-icon">
                <img src="/icons/FacebookIcon.svg" alt="Facebook" />
              </a>
            </div>
          </div>
          
          <div class="contact-info">
            <a href="mailto:bayudboutiqueresort@gmail.com" class="contact-email">
              bayudboutiqueresort@gmail.com
              <span class="underline"></span>
            </a>
          </div>
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
  
  // Create hover animations for menu items (excluding social icons)
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
      ease: 'expo.inOut',
      onComplete: () => {
        isAnimating.value = false
      }
    })
  } else {
    gsap.to(overlay.value, {
      y: '-100%',
      duration: 1.2,
      ease: 'expo.inOut',
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
  font-family: 'Vercetti', sans-serif;
  font-size: 1.5rem;
  font-weight: 400;
  color: #333333;
  text-decoration: none;
}

.navbar-right {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.book-button {
  background-color: transparent;
  color: #333333;
  border: 1px solid #333333;
  padding: 0.5rem 1.25rem;
  border-radius: 4px;
  font-family: 'Vercetti', sans-serif;
  font-size: 1rem;
  font-weight: 400;
  text-decoration: none;
  transition: background-color 0.2s ease, color 0.2s ease;
}

.book-button:hover {
  background-color: #333333;
  color: #ffffff;
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
  background-color: #2b3530;
  display: flex;
  z-index: 999;
}

.overlay-left {
  width: 45%;
  background-color: #222222;
}

.overlay-content {
  width: 55%;
  padding: 10rem 4rem 4rem 8rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.menu-links {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 1.5rem;
  margin-bottom: auto;
}

.overlay-item {
  color: #ffffff;
  font-family: 'Manrope', sans-serif;
  font-weight: 300;
  font-size: 2rem;
  text-decoration: none;
  display: inline-block;
  position: relative;
}

.underline {
  position: absolute;
  bottom: 0;
  left: 0;
  height: 1px;
  width: 0;
  display: block;
  background-color: #ffffff;
  opacity: 0.7;
}

.contact-section {
  margin-top: auto;
}

.social-links {
  margin-bottom: 0.5rem;
}

.social-icons {
  display: flex;
  gap: 0.5rem;
}

.social-icon {
  display: inline-block;
  transition: transform 0.2s ease;
}

.social-icon img {
  width: 24px;
  height: 24px;
}

.social-icon:hover {
  transform: scale(1.1);
}

.contact-info {
  color: #ffffff;
}

.contact-email {
  color: #aaaaaa;
  text-decoration: none;
  font-family: 'Manrope', sans-serif;
  font-weight: 400;
  font-size: 1rem;
  position: relative;
  display: inline-block;
  padding-bottom: 2px;
  transition: color 0.2s ease;
}

.contact-email:hover {
  color: #c8c8c8;
}

.contact-email .underline {
  background-color: #aaaaaa;
  opacity: 0.7;
}

.credits {
  color: #666666;
  font-family: 'Manrope', sans-serif;
  font-weight: 400;
  font-size: 0.750rem;
  margin-top: 1rem;
}

.credits a {
  color: #888888;
  text-decoration: none;
  position: relative;
  display: inline-block;
  padding-bottom: 1px;
  transition: color 0.2s ease;
}

.credits a:hover {
  color: #aaaaaa;
}

.credits a .underline {
  background-color: #888888;
  opacity: 0.7;
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
    padding: 10rem 2rem 2rem 2rem;
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
