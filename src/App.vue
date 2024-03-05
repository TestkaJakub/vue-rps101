
<script setup lang="ts">
import { ref, watch } from 'vue';
import Objects from './components/Objects.vue';
import axios from 'axios';

const ObjectsInfo = ref({
  choosenObject: '',
  allObjects: []
});
const outcome = ref({});

async function matchObjects(obj1 : string, obj2 : string) {
  let apiAdress = 'https://rps101.pythonanywhere.com/api/v1/match?';
  apiAdress += 'object_one=' + obj1 + '&object_two=' + obj2;
  
  await axios.get(apiAdress)
  .then(response => {
    outcome.value = response.data;
  })
  .catch(error => {
    console.log(error);
  });
}

watch(ObjectsInfo, (newValue) => {
  let allObjects = [];
  if(newValue.allObjects instanceof Array) allObjects = newValue.allObjects;
  else return;
  const randomObject = allObjects[Math.floor(Math.random() * allObjects.length)];

  let apiAdress = 'https://rps101.pythonanywhere.com/api/v1/match?';
  apiAdress += 'object_one=' + newValue.choosenObject + '&object_two=' + randomObject;

  matchObjects(newValue.choosenObject, randomObject);
});

function checkIfOutcomeIsDefined() {
  return Object.keys(outcome.value).length > 0;
}

function checkWhoWon() {
  if(outcome.value.winner == 'None - Draw is the same as None - Draw') return 'It\'s a draw!';
  else if(outcome.value.winner == ObjectsInfo.value.choosenObject) return 'You won!';
  else return 'You lost!';
}
</script>
<template>
  <h1>RPS 101</h1>
  <div v-if="checkIfOutcomeIsDefined()">
    <h2 v-if="checkWhoWon() == 'You won!'" class="won">{{ checkWhoWon() }}</h2>
    <h2 v-else-if="checkWhoWon() == 'You lost!'" class="lost">{{ checkWhoWon() }}</h2>
    <h2 v-else class="draw">{{ checkWhoWon() }}</h2>
    <h3>{{ outcome.winner }} {{ outcome.outcome }} {{ outcome.loser }}</h3>
  </div>
  <p>Choose an object</p>
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
