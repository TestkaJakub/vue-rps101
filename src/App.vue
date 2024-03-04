
<script setup lang="ts">
import Objects from './components/Objects.vue';
</script>
<template>
  <h1>RPS 101</h1>
  <Suspense>
    <template #default>
      <Objects />
    </template>
    <template #fallback>
      <p>Loading...</p>
    </template>
  </Suspense>
</template>

<style scoped>
</style>
