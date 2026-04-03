<script setup lang="ts">
import { ref } from 'vue'

// 1. 準備各種盒子與開關
const userName = ref('') // 裝你打的名字的盒子
const userData = ref<any>(null) // 裝大廚（GitHub）給的資料的外帶盒
const errorMsg = ref('') // 裝壞消息的小盒子（例如找不到人）
const isLoading = ref(false) // 服務生是不是正在跑腿的開關

// 2. 服務生出發找人的動作
const searchUser = async () => {
  // 先檢查盒子裡有沒有寫名字
  if (!userName.value.trim()) {
    errorMsg.value = '請先輸入名字喔！'
    userData.value = null
    return
  }

  // 開始跑腿，把開關打開
  isLoading.value = true
  errorMsg.value = ''
  userData.value = null

  try {
    // 服務生跑到 GitHub 大廚家問問題
    const response = await fetch(`https://api.github.com/users/${userName.value}`)
    
    // 如果大廚說找不到這個人
    if (!response.ok) {
      throw new Error('找不到這位偵察對象呢...')
    }

    // 大廚把資料裝進盤子裡（轉成 JSON）
    const data = await response.json()
    
    // 把資料放進我們的外帶盒
    userData.value = data
  } catch (err: any) {
    // 如果發生意外，把壞消息裝進盒子
    errorMsg.value = err.message
  } finally {
    // 服務生回來了，把開關關掉
    isLoading.value = false
  }
}
</script>

<template>
  <!-- 背景換成深色 -->
  <div class="min-h-screen bg-slate-900 py-12 px-4 sm:px-6 lg:px-8">
    <!-- 卡片也換成深色，但顏色比背景亮一點點 -->
    <div class="max-w-md mx-auto bg-slate-800 rounded-xl shadow-2xl overflow-hidden md:max-w-2xl p-8 border border-slate-700">
      <h1 class="text-3xl font-bold text-center text-white mb-8">GitHub 偵察機</h1>
      
      <div class="flex gap-2 mb-6">
        <!-- 綁定名字盒子，背景換成淺色讓字體清晰 -->
        <input 
          v-model="userName" 
          type="text" 
          placeholder="請輸入 GitHub 帳號"
          class="flex-1 px-4 py-2 bg-white border border-slate-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 text-slate-900 placeholder-slate-400"
          @keyup.enter="searchUser"
        />
        
        <!-- 按下按鈕，服務生出發 -->
        <button 
          :disabled="isLoading" 
          @click="searchUser"
          class="px-6 py-2 bg-blue-600 text-white font-semibold rounded-lg hover:bg-blue-700 disabled:bg-slate-600 disabled:cursor-not-allowed transition-all shadow-lg active:scale-95"
        >
          {{ isLoading ? '偵察中...' : '開始偵察' }}
        </button>
      </div>

      <!-- 顯示壞消息的小盒子 -->
      <div v-if="errorMsg" class="mb-6 p-4 bg-red-900/30 border-l-4 border-red-500 text-red-200">
        <p class="font-bold">提醒：</p>
        <p>{{ errorMsg }}</p>
      </div>
      
      <!-- 如果外帶盒有東西，就顯示出來 -->
      <div v-if="userData" class="mt-8 border-t border-slate-700 pt-8 flex flex-col items-center">
        <img :src="userData.avatar_url" alt="頭像" class="w-32 h-32 rounded-full border-4 border-blue-500/30 shadow-2xl mb-4" />
        <h2 class="text-2xl font-bold text-white">{{ userData.login }}</h2>
        <p class="text-slate-300 text-center mt-2 px-4 italic">
          {{ userData.bio || '這個偵察對象很神祕，沒有寫簡介。' }}
        </p>
        
        <div class="mt-6 flex gap-8 text-sm text-slate-400">
          <div class="text-center">
            <span class="block font-bold text-white text-xl">{{ userData.followers }}</span>
            追蹤者
          </div>
          <div class="text-center">
            <span class="block font-bold text-white text-xl">{{ userData.public_repos }}</span>
            公開倉庫
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* 這裡不需要額外的 CSS，因為我們用了 Tailwind！ */
</style>
