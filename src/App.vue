
<script setup lang="ts">

import { ref, watch, isProxy, toRaw, Suspense } from 'vue';
import axios from 'axios';
import Gestures from './components/Gestures.vue';

const gesturesInfo = ref({
  aG: [], // all Gestures
  rG: [], // random Gestures
  cG: '', // choosen Gesture
});
const outcome = ref<{ winner?: string, outcome?: string, loser?: string }>({});

async function matchObjects(gesture1 : string, gesture2 : string) {
  let apiAdress = 'https://rps101.pythonanywhere.com/api/v1/match?';
  apiAdress += 'object_one=' + gesture1 + '&object_two=' + gesture2;
  
  await axios.get(apiAdress)
  .then(response => {
    outcome.value = response.data;
    console.log(outcome.value)
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

</script>
<template>
  <h1>RPS 101</h1>
  <div v-if="isOutcomeDefined()">
    <h2 v-if="whoWon() == 'You won!'" class="won">{{ whoWon() }}</h2>
    <h2 v-else-if="whoWon() == 'You lost!'" class="lost">{{ whoWon() }}</h2>
    <h2 v-else class="draw">{{ whoWon() }}</h2>

    <h3 v-if="whoWon() != 'It\'s a draw!'">{{ outcome.winner }} {{ outcome.outcome }} {{ outcome.loser }}</h3>
    <h3 v-else>{{ gesturesInfo.cG }} {{ outcome.outcome }} {{ gesturesInfo.cG }}</h3>
    <div class="button-wrap">
      <button @click="outcome = {}">Play Again</button>
    </div>
  </div>
  <Suspense v-else>
    <template #default>
      <Gestures  v-model="gesturesInfo"></Gestures>
    </template>
    <template #fallback>
      <p>Loading...</p>
    </template>
  </Suspense>
</template>

<style scoped>
.won {
  color: #55ff55;
}
.lost {
  color: #ff5555;
}
.draw {
  color: #ffff55;
}
</style>
