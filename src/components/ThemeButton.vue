<script setup lang="ts">
import { ref } from 'vue';

const root = document.querySelector(':root');

function checkTheme(){
    // No root found, default to dark theme
    if (!root) return('dark');

    // Light theme
    if (root.classList.contains('light-theme')) return('light');

    // Dark theme
    else return('dark');
}

if (localStorage.getItem('theme') == null) {
  localStorage.setItem('theme', checkTheme());
}

const currentTheme = ref(localStorage.getItem('theme') as string);
console.log(currentTheme.value);

if(checkTheme() == 'light' && currentTheme.value == 'dark') {
    root?.classList.toggle('light-theme');
    currentTheme.value = 'light';
}else if(checkTheme() == 'dark' && currentTheme.value == 'light') {
    root?.classList.toggle('light-theme');
    currentTheme.value = 'dark';
}

function changeTheme() {
  if (root) {
    root.classList.toggle('light-theme');
    localStorage.setItem('theme', checkTheme());
    currentTheme.value = localStorage.getItem('theme') as string;
  }
}
</script>

<template>
  <button class="theme-button" @click="changeTheme">
    {{ currentTheme == 'light' ? '🌑' : '☀️'}}
   </button>
</template>

<style scoped>
.theme-button {
    position: absolute;
    top: 0;
    right: 0;
    padding: 10px;
    margin: 10px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    width: 80px;
    height: 80px;
    font-size: 40px;
}
</style>