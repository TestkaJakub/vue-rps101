
<script setup lang="ts">

import { ref, watch, isProxy, toRaw, Suspense } from 'vue';
import axios from 'axios';
import Gestures from './components/Gestures.vue';
import Controls from './components/Controls.vue';
import Infobox from './components/Infobox.vue';
import ThemeButton from './components/ThemeButton.vue';

const gesturesInfo = ref({
  aG: [], // all Gestures
  rG: [], // random Gestures
  cG: '', // choosen Gesture
});
const outcome = ref<{ winner?: string, outcome?: string, loser?: string }>({});

const gesturesEachRound = ref(5);
if(localStorage.getItem('gesturesEachRound') != null) 
  gesturesEachRound.value = Number(JSON.parse(localStorage.getItem('gesturesEachRound') as string));

async function matchObjects(gesture1 : string, gesture2 : string) {
  let apiAdress = 'https://rps101.pythonanywhere.com/api/v1/match?';
  apiAdress += 'object_one=' + gesture1 + '&object_two=' + gesture2;
  
  await axios.get(apiAdress)
  .then(response => {
    outcome.value = response.data;
  })
  .catch(error => {
    console.log(error);
  });
}

watch(gesturesInfo, (newValue) => {
  var gesturesInfoRaw;
  if(isProxy(newValue)){
    gesturesInfoRaw = toRaw(newValue);
  } else {
    gesturesInfoRaw = newValue;
  }

  let avaliableGestures = gesturesInfoRaw.rG;
  let randomGesture = avaliableGestures[Math.floor(Math.random() * avaliableGestures.length)];
  
  matchObjects(newValue.cG, randomGesture);
});

function isOutcomeDefined(){
  return Object.keys(outcome.value).length > 0;
}

function whoWon(){
  if(outcome.value.winner == 'None - Draw') return 'It\'s a draw!';
  else if(outcome.value.winner == gesturesInfo.value.cG) return 'You won!';
  else return 'You lost!';
}

function startNewRound(){
  sessionStorage.removeItem('randomGestures');
  outcome.value = {};
}

</script>
<template>
  <ThemeButton/>
  <img src="./assets/rpsp128.svg" alt="RPS 101 Logo" width="220"/>
  <div v-if="isOutcomeDefined()">
    <h2 v-if="whoWon() == 'You won!'" class="won">{{ whoWon() }}</h2>
    <h2 v-else-if="whoWon() == 'You lost!'" class="lost">{{ whoWon() }}</h2>
    <h2 v-else class="draw">{{ whoWon() }}</h2>

    <h3 v-if="whoWon() != 'It\'s a draw!'">{{ outcome.winner }} {{ outcome.outcome }} {{ outcome.loser }}</h3>
    <h3 v-else>{{ gesturesInfo.cG }} {{ outcome.outcome }} {{ gesturesInfo.cG }}</h3>
    <div class="button-wrap">
      <button @click="startNewRound">Play Again</button>
    </div>
    <Controls v-model="gesturesEachRound">
    </Controls>
  </div>
  <Suspense v-else>
    <template #default>
      <Gestures  v-model="gesturesInfo" :num-of-gestures="+gesturesEachRound"></Gestures>
    </template>
    <template #fallback>
      <p>Loading...</p>
    </template>
  </Suspense>
  <Infobox>
  </Infobox>
</template>

<style scoped>

</style>./components/Controls.vue
