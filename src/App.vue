<template>
  <div id="app">
    <div class="content-container">
      <h2>Create and Download File</h2>

      <div style="margin-bottom: 1rem" class="file-type-select">
        <label for="fileType">Select file type:</label>
        <select id="fileType" v-model="selectedExtension">
          <option v-for="type in fileTypes" :key="type" :value="type">
            {{ type }}
          </option>
        </select>
      </div>

      <div style="margin-bottom: 1rem">
        <label for="fileName">File name:</label>
        <input id="fileName" v-model="fileName" type="text" placeholder="Enter file name" style="width: 100%">
      </div>

      <div v-if="!isImageType" style="margin-bottom: 1rem">
        <label for="fileContent">File content:</label>
        <textarea id="fileContent" v-model="content" rows="6" style="width: 100%"></textarea>
      </div>

      <div v-else style="margin-bottom: 1rem">
        <p>Image preview (120px x 120px):</p>
        <div class="image-preview"></div>
      </div>

      <button class="download-button" @click="downloadFile">Download</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

const fileTypes = [
  'PDF', 'CSV', 'XLSX', 'XLS', 'JPG', 'JPEG', 'PNG', 'TIFF', 'HEIC',
  'DOCX', 'DOC', 'DOT', 'DOTX', 'RTF', 'TXT', 'JSON'
]
const selectedExtension = ref('TXT')
const content = ref('')
const fileName = ref('downloaded-file')

// Define file type categories
const imageTypes = ['JPG', 'JPEG', 'PNG', 'TIFF', 'HEIC']
const spreadsheetTypes = ['XLSX', 'XLS', 'CSV']

// Computed property to check if the selected type is an image
const isImageType = computed(() => {
  return imageTypes.includes(selectedExtension.value.toUpperCase())
})

const downloadFile = () => {
  // Use the user-provided file name or default to "downloaded-file" if empty
  const outputFileName = fileName.value.trim() || 'downloaded-file'
  
  if (isImageType.value) {
    // Generate a blank 120x120 image
    const canvas = document.createElement('canvas')
    canvas.width = 120
    canvas.height = 120
    const ctx = canvas.getContext('2d')

    if (ctx) {
      // Fill with light gray
      ctx.fillStyle = '#e0e0e0'
      ctx.fillRect(0, 0, canvas.width, canvas.height)

      // Add text indicating image type
      ctx.fillStyle = '#333333'
      ctx.font = '14px Arial'
      ctx.textAlign = 'center'
      ctx.fillText(`Sample ${selectedExtension.value}`, canvas.width/2, canvas.height/2)

      // Convert to blob and download
      canvas.toBlob((blob) => {
        if (blob) {
          const url = window.URL.createObjectURL(blob)
          const link = document.createElement('a')
          link.href = url
          link.download = `${outputFileName}.${selectedExtension.value.toLowerCase()}`
          link.click()
          window.URL.revokeObjectURL(url)
        }
      }, `image/${selectedExtension.value.toLowerCase() === 'jpg' ? 'jpeg' : selectedExtension.value.toLowerCase()}`)
    }
  } else if (spreadsheetTypes.includes(selectedExtension.value.toUpperCase())) {
    // For spreadsheet types, we'll create a CSV for simplicity
    // In a real app, you would use a library like SheetJS (xlsx) for Excel formats
    let fileContent = content.value
    if (selectedExtension.value.toUpperCase() === 'CSV' && !content.value.includes(',')) {
      // Add some CSV structure if user didn't provide it
      fileContent = "Column1,Column2,Column3\nData1,Data2,Data3\n" + content.value
    }

    const blob = new Blob([fileContent], {
      type: 'text/csv;charset=utf-8',
    })

    const url = window.URL.createObjectURL(blob)
    const link = document.createElement('a')
    link.href = url
    link.download = `${outputFileName}.${selectedExtension.value.toLowerCase()}`
    link.click()
    window.URL.revokeObjectURL(url)
  } else if (selectedExtension.value.toUpperCase() === 'PDF') {
    // For PDF, we'd normally use a library like jsPDF, but for simplicity
    // we'll just create a text file with a notice
    const pdfNotice = "This is a placeholder for PDF content.\n\n" + content.value +
                      "\n\nIn a real application, you would use a library like jsPDF to generate actual PDF files."

    const blob = new Blob([pdfNotice], {
      type: 'application/pdf',
    })

    const url = window.URL.createObjectURL(blob)
    const link = document.createElement('a')
    link.href = url
    link.download = `${outputFileName}.pdf`
    link.click()
    window.URL.revokeObjectURL(url)
  } else {
    // Handle all other document types - text-based formats
    const mimeTypes: Record<string, string> = {
      'TXT': 'text/plain',
      'HTML': 'text/html',
      'JSON': 'application/json',
      'MD': 'text/markdown',
      'DOCX': 'application/vnd.openxmlformats-officedocument.wordprocessingml.document',
      'DOC': 'application/msword',
      'DOT': 'application/msword',
      'DOTX': 'application/vnd.openxmlformats-officedocument.wordprocessingml.template',
      'RTF': 'application/rtf'
    }

    const mimeType = mimeTypes[selectedExtension.value.toUpperCase()] || 'text/plain'

    const blob = new Blob([content.value], {
      type: `${mimeType};charset=utf-8`,
    })

    const url = window.URL.createObjectURL(blob)
    const link = document.createElement('a')
    link.href = url
    link.download = `${outputFileName}.${selectedExtension.value.toLowerCase()}`
    link.click()
    window.URL.revokeObjectURL(url)
  }
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
  color: #000;
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

.download-button {
  padding: 10px 20px;
  border-radius: 5px;
  border: none;
  background-color: #000;
  color: #fff;
  cursor: pointer;
  font-weight: 700;
}

.image-preview {
  width: 120px;
  height: 120px;
  background-color: #e0e0e0;
  margin: 10px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 12px;
  color: #333;
  border: 1px solid #ccc;
  position: relative;
}

.image-preview::after {
  content: attr(data-extension);
  position: absolute;
}

.file-type-select {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
</style>
