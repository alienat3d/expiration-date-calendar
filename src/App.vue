<script setup>
// import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <div class="container mx-auto">
    <header>
      <!-- <nav>
          <RouterLink to="/">Home</RouterLink>
          <RouterLink to="/about">About</RouterLink>
        </nav> -->
    </header>
    <RouterView />
  </div>
</template>

<style>
@import 'tailwindcss';

body {
  @apply bg-gray-800;
  @apply text-amber-50;
  @apply text-xl;
}

h1 {
  @apply text-5xl;
  @apply font-medium;
}
</style>
