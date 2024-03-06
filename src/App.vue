
<script setup lang="ts">
import { ref, watch } from 'vue';
import Objects from './components/Objects.vue';
import axios from 'axios';

const ObjectsInfo = ref({
  choosenObject: '',
  allObjects: [],
  randomObjects: []
});
const outcome = ref<{ winner?: string, outcome?: string, loser?: string }>({});

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
  if(newValue.randomObjects instanceof Array) allObjects = newValue.randomObjects;
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
  if(outcome.value.winner == 'None - Draw') return 'It\'s a draw!';
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
    <h3 v-if="checkWhoWon() != 'It\'s a draw!'">{{ outcome.winner }} {{ outcome.outcome }} {{ outcome.loser }}</h3>
    <h3 v-else="checkWhoWon() != 'It\'s a draw!'">{{ ObjectsInfo.choosenObject }} {{ outcome.outcome }} {{ ObjectsInfo.choosenObject }}</h3>
    <div class="button-wrap">
      <button class="button" @click="outcome = {}">Play Again</button>
    </div>
  </div>
  
  <Suspense v-else>
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
.button {
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
.button-wrap {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  width: 100%;
}
</style>
