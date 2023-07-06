<template>
  <div>
    <h1>Upload Image</h1>
    <div
      class="drop-area"
      @dragover.prevent
      @dragenter.prevent
      @dragleave.prevent
      @drop="handleDrop"
      @click="handleClick"
    >
      <input
        type="file"
        ref="fileInput"
        @change="handleFileInputChange"
        accept="image/*"
        multiple
        style="display: none"
      />
      <p v-if="!files.length">Drag and drop images here, or click to browse</p>
      <div v-else>
        <p v-for="file in files" :key="file.name">{{ file.name }}</p>
      </div>
    </div>
    <button @click="uploadImages" :disabled="files.length === 0">Upload</button>
  </div>
</template>

<script>
export default {
  layout: 'default',
  data() {
    return {
      files: [],
    };
  },
  methods: {
    handleFileInputChange(event) {
      const fileList = event.target.files;
      this.files = Array.from(fileList);
    },
    handleDrop(event) {
      event.preventDefault();
      const fileList = event.dataTransfer.files;
      this.files = Array.from(fileList);
    },
    handleClick() {
      this.$refs.fileInput.click();
    },
    async uploadImages() {
      if (this.files.length === 0) {
        alert('Please select at least one image.');
        return;
      }

      try {
        for (const file of this.files) {
          const formData = new FormData();
          formData.append('file', file);
          formData.append('upload_preset', 'fynnyx');
          // Q: What is upload_preset?
          // A: Visit https://cloudinary.com/documentation/upload_images#upload_presets

          const response = await fetch('https://api.cloudinary.com/v1_1/dz2abhw86/image/upload', {
            method: 'POST',
            body: formData,
          });

          if (response.ok) {
            alert(`Image "${file.name}" uploaded successfully!`);
            // Do something with the response data if needed
            const responseData = await response.json();
            console.log(responseData);
          } else {
            alert(`Failed to upload image "${file.name}"`);
          }
        }
      } catch (error) {
        console.error('Error uploading images:', error);
        alert('An error occurred while uploading the images.');
      }
    },
  },
};
</script>


<style>
.drop-area {
  border: 2px dashed #ccc;
  padding: 20px;
  text-align: center;
  cursor: pointer;
}
</style>
