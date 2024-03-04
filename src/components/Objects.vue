
<script setup lang="ts">
import { ref, watch } from 'vue';
import axios from 'axios';

var allObjects : Array<string> = [];
const choosenObject = ref('');
const ObjectsInfo = ref(
    {
        choosenObject: choosenObject,
        allObjects: allObjects
    }
);

await axios.get('https://rps101.pythonanywhere.com/api/v1/objects/all')
.then(response => {
    allObjects = response.data;
    ObjectsInfo.value.allObjects = response.data;
})
.catch(error => {
    console.log(error);
});


const emit = defineEmits();

watch(choosenObject, (newValue) => {
    emit('update:modelValue', newValue);
    ObjectsInfo.value.choosenObject = newValue;
});
watch(ObjectsInfo, (newValue) => {
    emit('update:modelValue', newValue);
},{ deep: true});
</script>
<template>
    <div class="objects">
        <button class="object" v-for="object in allObjects" @click="choosenObject = object">
            <p >{{ object }}</p>
        </button>
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