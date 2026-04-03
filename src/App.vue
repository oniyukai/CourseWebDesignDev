<script setup lang="ts">
import { ref } from 'vue'

// 1. 準備一個空盒子，用來裝你打的名字
const userName = ref('') 

// 2. 準備一個小動作，按鈕按下後會執行
const searchUser = () => {
  alert('你剛才輸入了：' + userName.value)
}
</script>

<template>
  <div class="container">
    <h1>GitHub 偵察機</h1>
    
    <div class="search-box">
      <!-- 3. 把輸入框跟我們剛才準備的「盒子」綁在一起 -->
      <input 
        v-model="userName" 
        type="text" 
        placeholder="請輸入 GitHub 帳號"
      />
      
      <!-- 4. 按下按鈕時，鈴聲響起，執行 searchUser 動作 -->
      <button @click="searchUser">
        開始偵察
      </button>
    </div>
    
    <!-- 5. 測試用：把盒子裡的內容直接顯示出來看看 -->
    <p v-if="userName">
      你正在輸入：{{ userName }}
    </p>
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

button:hover {
  background-color: #33a06f;
}
</style>
