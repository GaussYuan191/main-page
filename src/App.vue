<script setup lang="ts">
// import ImgDisplay from "./components/ImgDisplay.vue";
import Album from "./components/Album.vue";
</script>

<template>
  <div>
    <Album />
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
