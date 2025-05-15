<script setup>
import StartScreen from "./components/StartScreen.vue";

const startQuiz = (topic) => {
  alert("start quiz! " + topic);
};
</script>

<template>
  <div id="app">
    <header>
      <div class="container">
        <img src="./assets/logo.png" alt="App logo" class="logo" />
        <h1>Quiz Generator</h1>
      </div>
    </header>
  </div>

  <StartScreen @start-quiz="startQuiz" />
</template>
