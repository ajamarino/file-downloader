<template>
  <div id="app">
    <div class="content-container">
      <h2>Create and Download File</h2>

      <div style="margin-bottom: 1rem">
        <label for="fileType">Select file type:</label>
        <select id="fileType" v-model="selectedExtension">
          <option v-for="type in fileTypes" :key="type" :value="type">
            {{ type }}
          </option>
        </select>
      </div>

      <div style="margin-bottom: 1rem">
        <label for="fileContent">File content:</label>
        <textarea id="fileContent" v-model="content" rows="6" style="width: 100%"></textarea>
      </div>

      <button @click="downloadFile">Download</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const fileTypes = ['txt', 'json', 'csv', 'md', 'html']
const selectedExtension = ref('txt')
const content = ref('')

const downloadFile = () => {
  const blob = new Blob([content.value], {
    type: 'text/plain;charset=utf-8',
  })

  const url = window.URL.createObjectURL(blob)
  const link = document.createElement('a')

  link.href = url
  link.download = `downloaded-file.${selectedExtension.value}`
  link.click()

  window.URL.revokeObjectURL(url)
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  width: 100%;
  overflow: hidden;
}

#app {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-image: url('https://secure.cmghomeloans.com/assets/img/background_gradient_img.svg');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

.content-container {
  background-color: rgba(255, 255, 255, 0.8);
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  width: 80%;
  max-width: 600px;
}

textarea {
  font-family: monospace;
}
</style>
