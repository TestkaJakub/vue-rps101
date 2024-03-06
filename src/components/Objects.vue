
<script setup lang="ts">
import { ref, watch, Ref } from 'vue';
import axios from 'axios';

var allObjects : Array<string> = [];
const choosenObject = ref('');
const randomObjects: Ref<string[]> = ref([]);
const ObjectsInfo = ref(
    {
        choosenObject: choosenObject,
        allObjects: allObjects,
        randomObjects: randomObjects
    }
);
const dummy = ref(0);

await axios.get('https://rps101.pythonanywhere.com/api/v1/objects/all')
.then(response => {
    allObjects = response.data;
    ObjectsInfo.value.allObjects = response.data;
})
.catch(error => {
    console.log(error);
});

async function getRandomObjects(numOfObjects : number) {
    let randomObjects = [];
    for(let i = 0; i < numOfObjects; i++) {
        randomObjects.push(allObjects[Math.floor(Math.random() * allObjects.length)]);
    }
    return randomObjects;
}

randomObjects.value = await getRandomObjects(5);

const emit = defineEmits();

 watch(() => [choosenObject.value, dummy.value], async (newValue) => {
    emit('update:modelValue', newValue[0]);
    ObjectsInfo.value.choosenObject = newValue[0];
    ObjectsInfo.value.randomObjects = randomObjects.value;

});
watch(() => [ObjectsInfo.value, dummy.value], (newValue) => {
    emit('update:modelValue', newValue[0]);
},{ deep: true});
function setObject(obj : string) {
    choosenObject.value = obj;
}
</script>
<template>
    <div>
        <p>Choose an object</p>
        <div class="objects">
            <button class="object" v-for="object in randomObjects" @click="setObject(object)">
                <p >{{ object }}</p>
            </button>
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
    height: 50px;
    width: 100px;
    border: 1px solid black;
    margin: 10px;
    border-radius: 5px;
    background-color: #f0f0f0;
    color: black;
}
</style>