<script setup>
import StartScreen from "./components/StartScreen.vue";
import Quiz from "./components/Quiz.vue";
import Loader from "./components/Loader.vue";
import Result from "./components/Result.vue";
import { GoogleGenerativeAI, SchemaType } from "@google/generative-ai";
import { ref } from "vue";

const questions = ref("");
const status = ref("start");
const userAnswers = ref([]);
const errorMessage = ref("");

const storeAnswer = (answer) => {
  userAnswers.value.push(answer);
};

const startQuiz = async (topic) => {
  status.value = "loading";
  const genAI = new GoogleGenerativeAI(import.meta.env.VITE_GEMINI_API);

  const schema = {
    description: "List of quiz questions",
    type: SchemaType.OBJECT,
    properties: {
      response_code: {
        type: SchemaType.NUMBER,
        description: "Response code indicating the status of the request",
        nullable: false,
      },
      results: {
        type: SchemaType.ARRAY,
        description: "Array of quiz question",
        items: {
          type: SchemaType.OBJECT,
          properties: {
            type: {
              type: SchemaType.STRING,
              description: "Type of question (e.g., multiple choice, boolean)",
              nullable: false,
            },
            difficulty: {
              type: SchemaType.STRING,
              description: "Difficulty level of the question (easy, medium, hard)",
              nullable: false,
            },
            category: {
              type: SchemaType.STRING,
              description: "The correct answer to the question",
              nullable: false,
            },
            question: {
              type: SchemaType.STRING,
              description: "The quiz question",
              nullable: false,
            },
            correct_answer: {
              type: SchemaType.STRING,
              description: "The correct answer to the question",
              nullable: false,
            },
            incorrect_answers: {
              type: SchemaType.ARRAY,
              description: "Array of incorrect answers",
              items: {
                type: SchemaType.STRING,
              },
              nullable: false,
            },
          },
          required: ["type", "difficulty", "category", "question", "correct_answer", "incorrect_answers"],
        },
        nullable: false,
      },
    },
    required: ["response_code", "results"],
  };

  const model = genAI.getGenerativeModel({
    model: "gemini-1.5-flash",
    generationConfig: {
      responseMimeType: "application/json",
      responseSchema: schema,
    },
  });

  try {
    const result = await model.generateContent(`Create 5 quiz questions about ${topic} Difficulty: Easy to Medium Type: Multiple Choice`);
    questions.value = JSON.parse(result.response.text());
    status.value = "ready";
  } catch (error) {
    errorMessage.value = error;
    status.value = "start";
  }
};

const restartQuiz = () => {
  questions.value = "";
  status.value = "start";
  userAnswers.value = [];
  errorMessage.value = "";
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

  <StartScreen v-if="status == 'start'" :errorMessage="errorMessage" @start-quiz="startQuiz" />
  <Loader v-if="status == 'loading'" />
  <Quiz v-if="status == 'ready'" @end-quiz="status = 'finished'" @store-answer="storeAnswer" :questions="questions" />
  <Result v-if="status == 'finished'" :userAnswers="userAnswers" @restart-quiz="restartQuiz" />
</template>
