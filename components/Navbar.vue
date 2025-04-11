<template>
    <nav class="navbar">
      <div class="navbar-container">
        <!-- Left: Brand -->
        <div class="navbar-brand">
          <NuxtLink to="/">Bayud</NuxtLink>
        </div>
  
        <!-- Right: Book Button & Hamburger -->
        <div class="navbar-right">
          <NuxtLink to="/book" class="book-button">Book</NuxtLink>
          <button class="menu-toggle" @click="toggleMenu" aria-label="Toggle menu">
            <img :src="menuOpen ? '/icons/CloseIcon.svg' : '/icons/NavbarIcon.svg'" alt="Menu Icon" />
          </button>
        </div>
      </div>
  
      <!-- Full-Screen Overlay Menu -->
      <div ref="overlay" class="overlay-menu" v-show="menuOpen">
        <div class="overlay-left">
          <!-- Placeholder for future images -->
        </div>
        <div class="overlay-right">
          <NuxtLink to="/accommodations" class="overlay-item" @click="closeMenu">Accommodations</NuxtLink>
          <NuxtLink to="/activities" class="overlay-item" @click="closeMenu">Activities</NuxtLink>
          <NuxtLink to="/book" class="overlay-item" @click="closeMenu">Book</NuxtLink>
        </div>
      </div>
    </nav>
  </template>
  
  <script setup>
  import { ref, watch, onMounted } from 'vue'
  import gsap from 'gsap'
  
  const menuOpen = ref(false)
  const overlay = ref(null)
  
  const toggleMenu = () => {
    menuOpen.value = !menuOpen.value
  }
  
  const closeMenu = () => {
    menuOpen.value = false
  }
  
  watch(menuOpen, (newVal) => {
    if (newVal) {
      // Ensure overlay is visible before animation
      gsap.set(overlay.value, { y: '-100%', display: 'flex' })
      gsap.to(overlay.value, {
        y: '0%',
        duration: 0.8,
        ease: 'power3.out'
      })
    } else {
      gsap.to(overlay.value, {
        y: '-100%',
        duration: 0.6,
        ease: 'power2.in',
        onComplete: () => {
          // Hide overlay after animation completes
          overlay.value.style.display = 'none'
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
    background: transparent;
    z-index: 1000;
  }
  
  .navbar-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1rem 2rem;
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
    gap: 1rem;
  }
  
  .book-button {
    background-color: #007BFF;
    color: #fff;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 4px;
    text-decoration: none;
    font-weight: 500;
  }
  
  .menu-toggle {
    background: none;
    border: none;
    cursor: pointer;
    padding: 8px;
    z-index: 1001; /* Ensure it's above overlay */
  }
  
  .menu-toggle img {
    width: 24px;
    height: 24px;
    transition: opacity 0.3s ease;
  }
  
  /* Overlay menu styles */
  .overlay-menu {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: #171716;
    display: none; /* Start hidden, GSAP will handle display */
    z-index: 999;
    overflow: hidden;
    transform: translateY(-100%); /* Start off-screen */
  }
  
  .overlay-left {
    width: 33.33%;
    background-color: #222; /* Placeholder background */
  }
  
  .overlay-right {
    width: 66.66%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 2rem;
  }
  
  .overlay-item {
    color: #fff;
    font-size: 2rem;
    text-decoration: none;
    margin: 1rem 0;
    padding: 0.5rem 1rem;
    opacity: 0; /* Start invisible for animation */
    transform: translateY(20px); /* Start slightly down */
  }
  
  .overlay-item:hover {
    text-decoration: underline;
  }
  </style>
  