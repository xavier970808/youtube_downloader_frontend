<template>
    <div class="container">
      <img src="logo" alt="YouTube logo" class="logo" />
      <h1>YouTube 影片下載器</h1>
      <input v-model="url" placeholder="輸入 YouTube 影片連結" />
      <button @click="download">下載影片</button>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  import logo from '@/assets/logo.png'
  
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
    let errorMessage = '下載失敗'
    try {
      const errorData = await response.json()
      errorMessage = errorData.error || errorMessage
    } catch {
      const text = await response.text()
      console.warn('非 JSON 錯誤回應：', text)
    }
    throw new Error(errorMessage)
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
    padding: 2rem;
    text-align: center;
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