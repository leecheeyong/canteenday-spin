<script setup>
  import Wheel from './components/wheel.vue'
</script>
<template>
  <Suspense>
    <template #default>
      <div class="flex h-screen">
        <div class="m-auto">
      <Wheel />
      </div>
      </div>
    </template>
    <template #fallback>
      <p class="text-2xl">Loading...</p>
    </template>
  </Suspense>
</template>
