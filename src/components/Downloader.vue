<template>
  <div class="container">
    <img :src="logo" alt="YouTube Logo" class="logo" />
    <h1>YouTube 影片下載器</h1>
    <input v-model="url" placeholder="輸入影片連結" />
    <button @click="download">下載影片</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import logo from '@/assets/youtube-logo.png'

const url = ref('')

const download = async () => {
  if (!url.value) {
    alert('請輸入影片連結')
    return
  }

  try {
    const response = await fetch(`${import.meta.env.VITE_API_URL}/download`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ url: url.value })
    })

    if (!response.ok) {
      let errMsg = '下載失敗'
      try {
        const json = await response.json()
        errMsg = json.error || errMsg
      } catch (e) {
        const text = await response.text()
        console.warn('非 JSON 錯誤回應：', text)
      }
      throw new Error(errMsg)
    }

    const blob = await response.blob()
    const link = document.createElement('a')
    link.href = window.URL.createObjectURL(blob)
    link.download = 'video.mp4'
    link.click()
  } catch (err) {
    alert(`錯誤：${err.message}`)
  }
}
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: auto;
  text-align: center;
  padding: 2rem;
}
.logo {
  width: 160px;
  margin-bottom: 1rem;
}
input {
  width: 80%;
  padding: 0.5rem;
  margin-bottom: 1rem;
}
button {
  padding: 0.5rem 1rem;
  cursor: pointer;
}
</style>