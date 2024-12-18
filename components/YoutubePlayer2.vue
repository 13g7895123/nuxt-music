<template>
    <div class="w-96 bg-gray-100 rounded-2xl p-6 shadow-lg">
      <div class="mb-6 text-center">
        <h2 class="text-xl font-semibold">{{ currentTrack.title }}</h2>
        <p class="text-gray-600">{{ currentTrack.artist }}</p>
      </div>
  
      <div class="relative mb-6">
        <div class="w-full h-1 bg-gray-300 rounded-full">
          <div class="w-1/2 h-1 bg-blue-500 rounded-full relative">
            <div class="absolute -top-1.5 -right-1.5 w-3 h-3 bg-blue-500 rounded-full"></div>
          </div>
        </div>
        <div class="flex justify-between text-xs text-gray-600 mt-2">
          <span>1:30</span>
          <span>{{ currentTrack.duration }}</span>
        </div>
      </div>
  
      <div class="flex items-center justify-center space-x-6">
        <button class="text-gray-600 hover:text-gray-800" @click="previousTrack">
          <Icon name="heroicons:backward" size="24" />
        </button>
        
        <button 
          class="bg-blue-500 text-white p-3 rounded-full shadow-md hover:bg-blue-600"
          @click="togglePlay"
        >
          <Icon :name="isPlaying ? 'heroicons:pause' : 'heroicons:play'" size="24" />
        </button>
        
        <button class="text-gray-600 hover:text-gray-800" @click="nextTrack">
          <Icon name="heroicons:forward" size="24" />
        </button>
  
        <button 
          class="text-gray-600 hover:text-red-500"
          :class="{ 'text-red-500': isFavorite }"
          @click="toggleFavorite"
        >
          <Icon 
            name="heroicons:heart" 
            size="20" 
            :class="{ 'fill-current': isFavorite }"
          />
        </button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  
  const isPlaying = ref(false)
  const isFavorite = ref(false)
  
  const currentTrack = ref({
    title: "Midnight Memory",
    artist: "Sonic Waves",
    album: "Urban Echoes", 
    duration: "3:45"
  })
  
  const togglePlay = () => {
    isPlaying.value = !isPlaying.value
  }
  
  const toggleFavorite = () => {
    isFavorite.value = !isFavorite.value
  }
  
  const previousTrack = () => {
    // 實現上一曲邏輯
    console.log('Previous track')
  }
  
  const nextTrack = () => {
    // 實現下一曲邏輯
    console.log('Next track')
  }
  </script>
  
  <style scoped>
  /* 可以添加額外的自定義樣式 */
  </style>