<script setup lang="ts">
import { ref, watch } from 'vue';

const gesturesEachRound = ref(5);
if(localStorage.getItem('gesturesEachRound') === null) {
    localStorage.setItem('gesturesEachRound', JSON.stringify(5));
}else{
    gesturesEachRound.value = Number(JSON.parse(localStorage.getItem('gesturesEachRound') as string));
}


const emit = defineEmits();

watch(gesturesEachRound, (newValue) => {
    emit('update:modelValue', newValue);
    localStorage.setItem('gesturesEachRound', JSON.stringify(newValue));
});
</script>
<template>
    <div>
        <h2>Controls</h2>
        <label for="gesturesEachRound"><h3>Number of gestures each round: {{ gesturesEachRound }}</h3></label>
        <input id="gesturesEachRound" type="range" min="2" max="101" v-model="gesturesEachRound" />
    </div>
</template>
<style scoped>

</style>