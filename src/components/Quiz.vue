<script setup>
import { ref, computed } from "vue";

const props = defineProps(["questions"]);

const currentQuestion = ref(0);

const shuffleOptions = computed(() => {
  let options = [...props.questions.results[currentQuestion.value].incorrect_answers];

  options.splice(Math.round(Math.random() * options.length), 0, props.questions.results[currentQuestion.value].correct_answer);

  return options;
});

console.log(props.questions.results[currentQuestion.value].correct_answer);

const submitAnswer = () => {
  currentQuestion.value++;
};
</script>

<template>
  <section>
    <h2>Quiz Component</h2>

    <div class="question">
      <h3>
        {{ props.questions.results[currentQuestion].question }}
      </h3>
    </div>

    <div class="answers">
      <button class="answer" v-for="answer in shuffleOptions" :key="answer">
        {{ answer }}
      </button>
    </div>

    <button @click="submitAnswer">Send</button>
  </section>
</template>
