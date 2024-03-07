<script setup lang="ts">
import { ref, watch } from 'vue';
import axios from 'axios';

const props = defineProps({
    numOfGestures: {
        type: Number,
        required: false,
        default: 5
    }
});

var allGestures : Array<string> = [];
var randomGestures: Array<string> = [];
var choosenGesture: string = '';

const gesturesInfo = ref(
    {
        aG: allGestures,
        rG: randomGestures,
        cG: choosenGesture
    }
);

const refresh = ref(0);

if(localStorage.getItem('allGestures') === null) {
    await axios.get('https://rps101.pythonanywhere.com/api/v1/objects/all')
    .then(response => {
        localStorage.setItem('allGestures', JSON.stringify(response.data));
    })
    .catch(error => {
        console.log(error);
    });
}

allGestures = JSON.parse(localStorage.getItem('allGestures') as string);

function getRandomGestures(numOfGestures : number) {
    let randomGestures = [];
    for(let i = 0; i < numOfGestures; i++) {
        randomGestures.push(allGestures[Math.floor(Math.random() * allGestures.length)]);
    }
    sessionStorage.setItem('randomGestures', JSON.stringify(randomGestures));
    return randomGestures;
}

if(sessionStorage.getItem('randomGestures') === null) {
    randomGestures = getRandomGestures(props.numOfGestures);
} else {
    randomGestures = JSON.parse(sessionStorage.getItem('randomGestures') as string);
}

const emit = defineEmits();

watch(() => [choosenGesture, refresh.value], (newValue) => {
    if (newValue !== null) {
        gesturesInfo.value.aG = allGestures;
        gesturesInfo.value.rG = randomGestures;
        gesturesInfo.value.cG = String(newValue[0]);
    }
});
watch(() => [gesturesInfo.value, refresh.value], (newValue) => {
    emit('update:modelValue', newValue[0]);
},{ deep: true});

function setGesture(gesture : string){
    refresh.value++;
    choosenGesture = gesture;
}
</script>
<template>
    <div>
        <h2>Choose a gesture</h2>
        <div class="gestures button-wrap">
            <button class="gesture" v-for="gesture in randomGestures" @click="setGesture(gesture)">
                <p>{{ gesture }}</p>
            </button>
        </div>
    </div>
</template>