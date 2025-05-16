<script setup>
const props = defineProps(["questions"]);

console.log(props.questions.results[0].question);
</script>

<template>
  <section>
    <h2>Quiz Component</h2>

    <div class="question">
      <h3>
        {{ props.questions.results[0].question }}
      </h3>
    </div>
  </section>
</template>
