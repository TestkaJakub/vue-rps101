
<script setup lang="ts">
import { ref, watch } from 'vue';
import Objects from './components/Objects.vue';

const ObjectsInfo = ref({
  choosenObject: '',
  allObjects: []
});

watch (ObjectsInfo, (newValue) => {
  console.log(newValue.allObjects);
});
</script>
<template>
  <h1>RPS 101</h1>
  <Suspense>
    <template #default>
      <Objects v-model="ObjectsInfo"/>
    </template>
    <template #fallback>
      <p>Loading...</p>
    </template>
  </Suspense>
</template>

<style scoped>
</style>
