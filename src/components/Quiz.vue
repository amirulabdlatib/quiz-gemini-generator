<script setup>
import { ref } from "vue";

const props = defineProps(["questions"]);

const currentQuestion = ref(0);

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

    <button @click="submitAnswer">Send</button>
  </section>
</template>
