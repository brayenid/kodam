<script setup>
  import Generate from './components/Generate.vue'
  import Title from './components/Title.vue'
  import Footer from './components/Footer.vue'
</script>
<template>
  <main class="flex justify-center">
    <div class="w-full max-w-3xl mt-6">
      <Title></Title>
      <Generate></Generate>
    </div>
  </main>
  <Footer></Footer>
</template>
