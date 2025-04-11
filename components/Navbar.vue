<template>
    <nav class="navbar">
      <div class="navbar-container">
        
        <div class="navbar-brand">
          <NuxtLink to="/">Bayud</NuxtLink>
        </div>
        
        <div class="navbar-right">
          <NuxtLink to="/book" class="book-button">Book</NuxtLink>
          <button class="menu-toggle" @click="toggleMenu" aria-label="Toggle menu">
            <img 
              :src="menuOpen ? '/icons/CloseIcon.svg' : '/icons/NavbarIcon.svg'" 
              alt="Menu Icon" 
            />
          </button>
        </div>
      </div>
  
      <div ref="overlay" class="overlay-menu" :style="{ visibility: overlayVisible ? 'visible' : 'hidden' }">
        <div class="overlay-left"></div>
        <div class="overlay-content">
          <div class="menu-links">
            <NuxtLink to="/book" class="overlay-item" @click="closeMenu">Book</NuxtLink>
            <NuxtLink to="/activities" class="overlay-item" @click="closeMenu">Activities</NuxtLink>
            <NuxtLink to="/accommodations" class="overlay-item" @click="closeMenu">Accommodations</NuxtLink>
          </div>
          
          <div class="contact-info">
            <div class="contact-title">Contact us</div>
            <a href="mailto:bayudboutiqueresort@gmail.com" class="contact-email">bayudboutiqueresort@gmail.com</a>
          </div>
          
          <div class="credits">
            Site created by <a href="https://morrowlab.studio" target="_blank" rel="noopener">Morrowlab Studio</a>
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
      y: '-100%',
      opacity: 0
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
        opacity: 1,
        duration: 0.8,
        ease: 'power3.out',
        onComplete: () => {
          isAnimating.value = false
        }
      })
    } else {
      gsap.to(overlay.value, {
        y: '-100%',
        opacity: 0,
        duration: 0.6,
        ease: 'power2.in',
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
    transition: color 0.2s ease;
  }
  
  .navbar-brand a:hover {
    color: #007BFF;
  }
  
  .navbar-right {
    display: flex;
    align-items: center;
    gap: 1.5rem;
  }
  
  .book-button {
    background-color: #007BFF;
    color: #fff;
    padding: 0.5rem 1.25rem;
    border-radius: 4px;
    text-decoration: none;
    font-weight: 500;
    transition: all 0.2s ease;
  }
  
  .book-button:hover {
    background-color: #0069d9;
    transform: translateY(-1px);
  }
  
  .menu-toggle {
    background: none;
    border: none;
    cursor: pointer;
    padding: 0.5rem;
    z-index: 1001;
  }
  
  .menu-toggle img {
    width: 24px;
    height: 24px;
    transition: transform 0.2s ease;
  }
  
  .menu-toggle:hover img {
    transform: scale(1.1);
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
    will-change: transform;
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
    position: relative;
    transition: color 0.2s ease;
  }
  
  .overlay-item:hover {
    color: #007BFF;
  }
  
  .contact-info {
    margin-top: auto;
    color: white;
  }
  
  .contact-title {
    font-size: 1.25rem;
    margin-bottom: 0.5rem;
  }
  
  .contact-email {
    color: #aaa;
    text-decoration: none;
    font-size: 1rem;
    transition: color 0.2s ease;
  }
  
  .contact-email:hover {
    color: #007BFF;
  }
  
  .credits {
    color: #666;
    font-size: 0.875rem;
    margin-top: 2rem;
  }
  
  .credits a {
    color: #888;
    text-decoration: none;
    transition: color 0.2s ease;
  }
  
  .credits a:hover {
    color: #007BFF;
  }
  
  /* Responsive adjustments */
  @media (max-width: 768px) {
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
  }
  </style>
