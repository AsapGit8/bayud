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
  
      <!-- Dropdown Menu -->
      <div v-if="menuOpen" class="dropdown-menu">
        <NuxtLink to="/accommodations" class="dropdown-item">Accommodations</NuxtLink>
        <NuxtLink to="/activities" class="dropdown-item">Activities</NuxtLink>
        <NuxtLink to="/book" class="dropdown-item">Book</NuxtLink>
      </div>
    </nav>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  
  const menuOpen = ref(false)
  const toggleMenu = () => {
    menuOpen.value = !menuOpen.value
  }
  </script>
  
  <style scoped>
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
  }
  
  .menu-toggle img {
    width: 24px;
    height: 24px;
  }
  
  .dropdown-menu {
    position: absolute;
    top: 100%;
    right: 2rem;
    background-color: #fff;
    border-radius: 4px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    margin-top: 0.5rem;
    display: flex;
    flex-direction: column;
  }
  
  .dropdown-item {
    padding: 0.75rem 1rem;
    color: #333;
    text-decoration: none;
  }
  
  .dropdown-item:hover {
    background-color: #f0f0f0;
  }
  </style>
  