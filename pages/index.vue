<template>
    <div>
      <LoadingScreen v-if="!loaded" @done="loaded = true" />
      <div v-else class="main-content">
        <h1>This is the index.vue page</h1>
        <!-- Your page content here -->
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, watch } from 'vue'
  import LoadingScreen from '@/components/LoadingScreen.vue'
  
  const loaded = ref(false)
  </script>
  