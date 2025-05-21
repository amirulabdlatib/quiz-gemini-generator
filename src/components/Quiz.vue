<script setup>
import { ref, computed } from "vue";

const props = defineProps(["questions"]);
const emit = defineEmits(["store-answer", "end-quiz"]);

const currentQuestion = ref(0);
const selectedOption = ref(null);

const shuffleOptions = computed(() => {
  let options = [...props.questions.results[currentQuestion.value].incorrect_answers];

  options.splice(Math.round(Math.random() * options.length), 0, props.questions.results[currentQuestion.value].correct_answer);

  return options;
});

const submitAnswer = () => {
  emit("store-answer", {
    question: props.questions.results[currentQuestion.value],
    userAnswer: selectedOption.value,
  });
  selectedOption.value = null;

  if (currentQuestion.value === props.questions.results.length - 1) {
    currentQuestion.value = 0;
    emit("end-quiz");
  } else {
    currentQuestion.value++;
  }
};
</script>

<template>
  <section>
    <div class="header">
      <h2>Quiz Component</h2>
      <p>Question {{ currentQuestion + 1 }} of {{ props.questions.results.length }}</p>
    </div>
    <progress max="100" :value="((currentQuestion + 1) / props.questions.results.length) * 100"></progress>

    <div class="question">
      <h3>
        {{ props.questions.results[currentQuestion].question }}
      </h3>
    </div>

    <div class="answers">
      <button class="answer" :class="{ active: answer === selectedOption }" v-for="answer in shuffleOptions" :key="answer" @click="selectedOption = answer">
        {{ answer }}
      </button>
    </div>

    <button @click="submitAnswer" v-if="selectedOption">Send</button>
  </section>
</template>
