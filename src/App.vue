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
  <div class="container">
    <h1>GitHub 偵察機</h1>
    
    <div class="search-box">
      <!-- 綁定名字盒子 -->
      <input 
        v-model="userName" 
        type="text" 
        placeholder="請輸入 GitHub 帳號"
        @keyup.enter="searchUser"
      />
      
      <!-- 按下按鈕，服務生出發 -->
      <button :disabled="isLoading" @click="searchUser">
        {{ isLoading ? '偵察中...' : '開始偵察' }}
      </button>
    </div>

    <!-- 顯示壞消息的小盒子 -->
    <p v-if="errorMsg" class="error">
      {{ errorMsg }}
    </p>
    
    <!-- 第四步預留：如果外帶盒有東西，就顯示出來 -->
    <div v-if="userData" class="result">
      <img :src="userData.avatar_url" alt="頭像" class="avatar" />
      <h2>{{ userData.login }}</h2>
      <p>{{ userData.bio || '這個偵察對象很神祕，沒有寫簡介。' }}</p>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 600px;
  margin: 50px auto;
  text-align: center;
  font-family: sans-serif;
}

.search-box {
  margin: 20px 0;
}

input {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-right: 10px;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:disabled {
  background-color: #999;
  cursor: not-allowed;
}

.error {
  color: #ff4d4f;
  font-weight: bold;
}

.result {
  margin-top: 30px;
  padding: 20px;
  border: 1px solid #eee;
  border-radius: 8px;
}

.avatar {
  width: 150px;
  height: 150px;
  border-radius: 50%;
  border: 3px solid #42b883;
}
</style>
