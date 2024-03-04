
<script setup lang="ts">
import { defineComponent, ref, Suspense } from 'vue';
import axios, { all } from 'axios';

const allObjects = ref([]);

await axios.get('https://rps101.pythonanywhere.com/api/v1/objects/all')
    .then(response => {
        allObjects.value = response.data;
    })
    .catch(error => {
        console.log(error);
    });
</script>
<template>
    <div class="objects">
        <div class="object" v-for="object in allObjects">
            <p >{{ object }}</p>
        </div>
    </div>
</template>

<style scoped>
.objects {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    height: 100%;
    width: 100%;
    color: black;
}
.object {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 30px;
    width: 100px;
    border: 1px solid black;
    margin: 10px;
    border-radius: 5px;
    background-color: #f0f0f0;
    color: black;
}
</style>