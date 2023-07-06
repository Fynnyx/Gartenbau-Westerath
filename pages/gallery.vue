<template>
  <div>
    <h1>Image Gallery</h1>
    <div v-if="loading">Loading images...</div>
    <div v-else>
      <div v-for="image in images" :key="image.public_id">
        <img
          :src="generateImageUrl(image.url)"
          :alt="image.public_id"
          style="max-width: 300px; margin-bottom: 10px"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  layout: "default",
  ssr: true,
  data() {
    return {
      images: [],
      loading: false,
    };
  },
  mounted() {
    this.fetchImages();
  },
  methods: {
    async fetchImages() {
      this.loading = true;

      try {
        const response = await fetch(
          "https://api.cloudinary.com/v1_1/dz2abhw86/resources/image?type=upload&max_results=100",
          {
            method: "GET",
            headers: {
              "Content-Type": "application/json",
              Authorization: "Basic " + btoa("742371634793824:FnlAATzZws3C1psfK--IZVd2d1s"),
            },
          }
        );

        if (response.ok) {
          const responseData = await response.json();
          this.images = responseData.resources;
        } else {
          console.error("Failed to fetch images:", response.statusText);
        }
      } catch (error) {
        console.error("Error fetching images:", error);
      }

      this.loading = false;
    },
    generateImageUrl(url) {
      const minWidth = 200; // Minimum width for random width and height
      const maxWidth = 400; // Maximum width for random width and height
      const minHeight = 200; // Minimum height for random width and height
      const maxHeight = 400; // Maximum height for random width and height
      
      const width = Math.floor(Math.random() * (maxWidth - minWidth + 1)) + minWidth;
      const height = Math.floor(Math.random() * (maxHeight - minHeight + 1)) + minHeight;
      url = url.replace("upload/", `upload/w_${width},h_${height},c_limit,e_blur:400,o_90,b_black/l_text:arial_80:®,ar_1:1,c_lfill,o_60,co_rgb:ffffff,b_rgb:000000,r_max/`);
      return url
    },
  },
};
</script>
