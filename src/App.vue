<script setup>
import StartScreen from "./components/StartScreen.vue";
import { GoogleGenerativeAI } from "@google/generative-ai";
import { ref } from "vue";

const question = ref("");

const startQuiz = async (topic) => {
  question.value = "Loading question...";

  const genAI = new GoogleGenerativeAI(import.meta.env.VITE_GEMINI_API);
  const model = genAI.getGenerativeModel({ model: "gemini-1.5-flash" });
  const prompt = `Create multi-choice quiz question about ${topic}`;
  const result = await model.generateContent(prompt);

  question.value = result.response.text();
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

  <p>{{ question }}</p>
</template>
