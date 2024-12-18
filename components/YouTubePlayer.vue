<!-- components/ModernYouTubePlayer.vue -->
<template>
    <div class="modern-youtube-player-container bg-gray-900 text-white rounded-xl shadow-2xl overflow-hidden max-w-3xl mx-auto">
      <!-- 播放器容器 -->
      <div class="player-wrapper relative">
        <div ref="youtubePlayer" class="youtube-player"></div>
        
        <!-- 覆蓋層 -->
        <div 
          v-if="!isPlaying" 
          class="absolute inset-0 bg-black bg-opacity-50 flex items-center justify-center cursor-pointer"
          @click="playVideo"
        >
          <svg xmlns="http://www.w3.org/2000/svg" class="h-24 w-24 text-white" fill="currentColor" viewBox="0 0 24 24">
            <path d="M8 5v14l11-7z"/>
          </svg>
        </div>
      </div>
  
      <!-- 控制面板 -->
      <div class="controls-panel bg-gray-800 p-4 flex items-center space-x-4">
        <!-- 播放/暫停 -->
        <button 
          @click="togglePlay" 
          class="control-btn hover:bg-gray-700 p-2 rounded-full transition-colors"
        >
          <svg v-if="isPlaying" xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
            <path d="M6 19h4V5H6v14zm8-14v14h4V5h-4z"/>
          </svg>
          <svg v-else xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="currentColor" viewBox="0 0 24 24">
            <path d="M8 5v14l11-7z"/>
          </svg>
        </button>
  
        <!-- 上一首/下一首 -->
        <div class="flex space-x-2">
          <button 
            @click="playPrevious" 
            class="control-btn hover:bg-gray-700 p-2 rounded-full transition-colors"
          >
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="currentColor" viewBox="0 0 24 24">
              <path d="M11 18V6l-8.5 6 8.5 6zm1-6l8.5 6V6l-8.5 6z"/>
            </svg>
          </button>
          <button 
            @click="playNext" 
            class="control-btn hover:bg-gray-700 p-2 rounded-full transition-colors"
          >
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="currentColor" viewBox="0 0 24 24">
              <path d="M4 18l8.5-6L4 6v12zm9-12v12l8.5-6-8.5-6z"/>
            </svg>
          </button>
        </div>
  
        <!-- 進度條 -->
        <div class="flex-grow flex items-center space-x-3">
          <span class="text-sm">{{ formatTime(currentTime) }}</span>
          <input 
            type="range" 
            :value="currentTime" 
            :max="videoDuration" 
            @input="seekVideo"
            class="progress-slider flex-grow h-2 rounded-full appearance-none bg-gray-700 mb-2.5"
          />
          <span class="text-sm">{{ formatTime(videoDuration) }}</span>
        </div>
  
        <!-- 音量 -->
        <div class="flex items-center space-x-2">
          <button 
            @click="toggleMute" 
            class="control-btn hover:bg-gray-700 p-2 rounded-full transition-colors"
          >
            <svg v-if="isMuted" xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="currentColor" viewBox="0 0 24 24">
              <path d="M16.5 12c0-1.77-1.02-3.29-2.5-4.03v2.21l2.45 2.45c.03-.2.05-.41.05-.63zm2.5 0c0 .94-.2 1.82-.54 2.64l1.51 1.51C20.63 14.91 21 13.5 21 12c0-4.28-2.99-7.86-7-8.77v2.06c2.89.86 5 3.54 5 6.71zM4.27 3L3 4.27 7.73 9H3v6h4l5 5v-6.73l4.25 4.25c-.67.52-1.42.93-2.25 1.18v2.06c1.38-.31 2.63-.95 3.69-1.81L19.73 21 21 19.73l-9-9L4.27 3zM12 4L9.91 6.09 12 8.18V4z"/>
            </svg>
            <svg v-else xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="currentColor" viewBox="0 0 24 24">
              <path d="M3 9v6h4l5 5V4L7 9H3zm13.5 3c0-1.77-1.02-3.29-2.5-4.03v8.05c1.48-.73 2.5-2.25 2.5-4.02zM14 3.23v2.06c2.89.86 5 3.54 5 6.71s-2.11 5.85-5 6.71v2.06c4.01-.91 7-4.49 7-8.77s-2.99-7.86-7-8.77z"/>
            </svg>
          </button>
          <input 
            type="range" 
            :value="volumeLevel" 
            min="0" 
            max="100" 
            @input="changeVolume"
            class="volume-slider w-24 h-2 rounded-full appearance-none bg-gray-700"
          />
        </div>
      </div>
  
      <!-- 播放列表 -->
      <div class="playlist-section bg-gray-800 p-4 border-t border-gray-700">
        <div v-if="isAuth == false" class="flex mb-4">
          <input 
            v-model="newVideoInput" 
            @keyup.enter="verifyIdentify"
            placeholder="輸入使用者 Key" 
            class="flex-grow p-2 bg-gray-700 rounded-l text-white"
          >
          <button 
            @click="verifyIdentify" 
            class="bg-green-500 hover:bg-green-600 text-white px-4 rounded-r transition-colors"
          >
            驗證
          </button>
        </div>
        <div v-else class="flex mb-4">
          <input 
            v-model="newVideoInput" 
            @keyup.enter="addToPlaylist"
            placeholder="輸入 YouTube 影片 ID 或連結" 
            class="flex-grow p-2 bg-gray-700 rounded-l text-white"
          >
          <button 
            @click="addToPlaylist" 
            class="bg-blue-600 hover:bg-blue-700 text-white px-4 rounded-r transition-colors"
          >
            加入
          </button>
        </div>
  
        <div v-if="playlist.length" class="playlist-list max-h-60 overflow-y-auto">
          <div 
            v-for="(video, index) in playlist" 
            :key="video.id" 
            class="playlist-item flex items-center justify-between p-2 hover:bg-gray-700 rounded transition-colors cursor-pointer"
            :class="{ 'bg-blue-900': currentPlaylistIndex === index }"
            @click="playFromPlaylist(index)"
          >
            <div class="flex items-center space-x-3">
              <img 
                :src="`https://img.youtube.com/vi/${video.id}/default.jpg`" 
                class="w-16 h-12 object-cover rounded"
                alt="影片縮圖"
              />
              <span class="text-sm">{{ video.title || video.id }}</span>
            </div>
            <button 
              @click.stop="removeFromPlaylist(index)" 
              class="text-red-500 hover:text-red-400 p-1"
            >
              ✖
            </button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, onUnmounted, watch } from 'vue'
  
  // 狀態管理
  const playlist = ref([])
  const currentPlaylistIndex = ref(0)
  const newVideoInput = ref('')
  const youtubePlayer = ref(null)
  const player = ref(null)
  const isAuth = ref(false);
  
  // 播放器狀態
  const isPlaying = ref(false)
  const isMuted = ref(false)
  const volumeLevel = ref(50)
  const currentTime = ref(0)
  const videoDuration = ref(0)
  
  // 載入 YouTube API
  const loadYouTubeAPI = () => {
    return new Promise((resolve, reject) => {
      if (window.YT) {
        resolve(window.YT)
        return
      }
  
      const tag = document.createElement('script')
      tag.src = 'https://www.youtube.com/iframe_api'
      const firstScriptTag = document.getElementsByTagName('script')[0]
      firstScriptTag.parentNode.insertBefore(tag, firstScriptTag)
  
      window.onYouTubeIframeAPIReady = () => {
        resolve(window.YT)
      }
    })
  }
  
  // 初始化播放器
  const initPlayer = async () => {
    try {
      const YT = await loadYouTubeAPI()
      
      // 預設影片
      const defaultVideoId = 'dQw4w9WgXcQ'
      playlist.value.push({ id: defaultVideoId })
  
      player.value = new YT.Player(youtubePlayer.value, {
        height: '480',
        width: '100%',
        videoId: defaultVideoId,
        playerVars: {
          'autoplay': 0,
          'controls': 0
        },
        events: {
          'onReady': onPlayerReady,
          'onStateChange': onPlayerStateChange
        }
      })
    } catch (error) {
      console.error('YouTube Player 初始化失敗:', error)
    }
  }
  
  // 播放器準備就緒
  const onPlayerReady = (event) => {
    videoDuration.value = event.target.getDuration()
    
    // 定期更新播放進度
    setInterval(() => {
      if (player.value) {
        currentTime.value = player.value.getCurrentTime()
        isPlaying.value = player.value.getPlayerState() === window.YT.PlayerState.PLAYING
      }
    }, 1000)
  }
  
  // 播放器狀態變化
  const onPlayerStateChange = (event) => {
    // 當影片播放結束時自動播放下一首
    if (event.data === window.YT.PlayerState.ENDED) {
      playNext()
    }
  }
  
  // 播放控制
  const togglePlay = () => {
    if (isPlaying.value) {
      pauseVideo()
    } else {
      playVideo()
    }
  }
  
  const playVideo = () => {
    player.value?.playVideo()
    isPlaying.value = true
  }
  
  const pauseVideo = () => {
    player.value?.pauseVideo()
    isPlaying.value = false
  }
  
  // 音量控制
  const toggleMute = () => {
    isMuted.value = !isMuted.value
    player.value?.isMuted() ? player.value.unMute() : player.value.mute()
  }
  
  const changeVolume = (event) => {
    const volume = parseInt(event.target.value)
    volumeLevel.value = volume
    player.value?.setVolume(volume)
    isMuted.value = volume === 0
  }
  
  // 進度控制
  const seekVideo = (event) => {
    const time = parseInt(event.target.value)
    player.value?.seekTo(time)
    currentTime.value = time
  }
  
  // 播放列表管理
  const addToPlaylist = async () => {
    // 支援完整 YouTube 連結和影片 ID
    const match = newVideoInput.value.match(/(?:https?:\/\/)?(?:www\.)?(?:youtube\.com\/(?:[^\/\n\s]+\/\S+\/|(?:v|e(?:mbed)?)\/|\S*?[?&]v=)|youtu\.be\/)([a-zA-Z0-9_-]{11})/)
    const videoId = match ? match[1] : newVideoInput.value
  
    // 獲取影片標題
    try {
      const response = await fetch(`https://youtube.com/oembed?url=https://www.youtube.com/watch?v=${videoId}&format=json`)
      const data = await response.json()
      
      playlist.value.push({
        id: videoId,
        title: data.title
      })
      
      newVideoInput.value = '' // 清空輸入
    } catch (error) {
      // 如果無法獲取標題，只添加影片 ID
      playlist.value.push({
        id: videoId,
        title: videoId
      })
      newVideoInput.value = '' // 清空輸入
    }
  }
  
  const removeFromPlaylist = (index) => {
    playlist.value.splice(index, 1)
    
    // 調整當前播放索引
    if (currentPlaylistIndex.value >= playlist.value.length) {
      currentPlaylistIndex.value = playlist.value.length - 1
    }
  }
  
  const playFromPlaylist = (index) => {
    currentPlaylistIndex.value = index
    loadVideoById(playlist.value[index].id)
  }
  
  const playNext = () => {
    if (playlist.value.length === 0) return
  
    // 循環播放
    currentPlaylistIndex.value = (currentPlaylistIndex.value + 1) % playlist.value.length
    loadVideoById(playlist.value[currentPlaylistIndex.value].id)
  }
  
  const playPrevious = () => {
    if (playlist.value.length === 0) return
  
    // 循環播放
    currentPlaylistIndex.value = (currentPlaylistIndex.value - 1 + playlist.value.length) % playlist.value.length
    loadVideoById(playlist.value[currentPlaylistIndex.value].id)
  }
  
  // 載入影片的通用方法
  const loadVideoById = (id) => {
    if (player.value) {
      player.value.loadVideoById(id)
      isPlaying.value = true
    }
  }
  
  // 時間格式化
  const formatTime = (seconds) => {
    const minutes = Math.floor(seconds / 60)
    const remainingSeconds = Math.floor(seconds % 60)
    return `${minutes}:${remainingSeconds < 10 ? '0' : ''}${remainingSeconds}`
  }
  
  // 組件生命週期
  onMounted(initPlayer)
  onUnmounted(() => player.value?.destroy())
  </script>
  
  <style scoped>
/* 範圍滑塊自訂樣式 */
.progress-slider::-webkit-slider-thumb,
.volume-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 16px;
  height: 16px;
  background: #4299e1; /* 藍色 */
  cursor: pointer;
  border-radius: 50%;
  transition: background 0.15s ease-in-out;
}

.progress-slider::-webkit-slider-thumb:hover,
.volume-slider::-webkit-slider-thumb:hover {
  background: #3182ce; /* 深藍色 */
}

.progress-slider::-moz-range-thumb,
.volume-slider::-moz-range-thumb {
  width: 16px;
  height: 16px;
  background: #4299e1;
  cursor: pointer;
  border-radius: 50%;
  border: none;
  transition: background 0.15s ease-in-out;
}

.progress-slider::-moz-range-thumb:hover,
.volume-slider::-moz-range-thumb:hover {
  background: #3182ce;
}

/* 滑塊軌道 */
.progress-slider::-webkit-slider-runnable-track,
.volume-slider::-webkit-slider-runnable-track {
  width: 100%;
  height: 8px;
  background: #4a5568; /* 灰色軌道 */
  border-radius: 4px;
}

.progress-slider::-moz-range-track,
.volume-slider::-moz-range-track {
  width: 100%;
  height: 8px;
  background: #4a5568;
  border-radius: 4px;
}

/* 隱藏原生控制項 */
.progress-slider,
.volume-slider {
  -webkit-appearance: none;
  appearance: none;
  background: transparent;
  outline: none;
}

/* 互動效果 */
.control-btn {
  transition: all 0.2s ease-in-out;
}

.control-btn:hover {
  transform: scale(1.1);
  opacity: 0.8;
}

/* 播放列表捲軸 */
.playlist-list::-webkit-scrollbar {
  width: 8px;
}

.playlist-list::-webkit-scrollbar-track {
  background: #2d3748; /* 深灰背景 */
}

.playlist-list::-webkit-scrollbar-thumb {
  background: #4a5568; /* 滾動條顏色 */
  border-radius: 4px;
}

.playlist-list::-webkit-scrollbar-thumb:hover {
  background: #718096; /* 懸停時的顏色 */
}
</style>