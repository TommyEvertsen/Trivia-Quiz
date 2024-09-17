<template>
  <v-card class="mx-auto" max-width="344" v-if="getEasyAllCategoryQuestion">
    <v-card-text>
      <div class="font-weight-black">Trivia question</div>

      <p class=" ">Category: {{ category }}</p>

      <p>Difficulty: {{ difficulty }}</p>

      <p>Type: {{ type }}</p>

      <div class="text-medium-emphasis">
        {{ question }}
      </div>

      <div v-if="type === 'multiple'">
        <div v-for="(answer, index) in shuffledAnswers" :key="index">
          {{ answer }}
        </div>
      </div>
    </v-card-text>

    <v-card-actions>
      <v-btn
        color="teal-accent-4"
        text="Show answer"
        variant="text"
        @click="reveal = true"
      ></v-btn>
    </v-card-actions>

    <v-expand-transition>
      <v-card
        v-if="reveal"
        class="position-absolute w-100"
        height="100%"
        style="bottom: 0"
      >
        <v-card-text class="pb-0">
          <p class="text-h4">Answer</p>

          <p class="text-medium-emphasis">
            {{ correctAnswer }}
          </p>
        </v-card-text>

        <v-card-actions class="pt-0">
          <v-btn
            color="teal-accent-4"
            text="Close"
            variant="text"
            @click="reveal = false"
          ></v-btn>
        </v-card-actions>
      </v-card>
    </v-expand-transition>
  </v-card>

  <v-btn @click="getEasyAllCategoryQuestion">Get question</v-btn>
</template>

<script setup>
import { ref } from "vue";

const reveal = ref(false);

const category = ref("");
const question = ref("");
const correctAnswer = ref("");
const incorrectAnswers = ref([]);
const type = ref("");
const difficulty = ref("");
const shuffledAnswers = ref([]);

watch(type, (newValue) => {
  if (newValue === "boolean") {
    type.value = "True or false";
  }
});

function shuffleArray(array) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
  return array;
}
const getEasyAllCategoryQuestion = () => {
  fetch("https://opentdb.com/api.php?amount=1&difficulty=easy")
    .then((response) => response.json())
    .then((data) => {
      const trivia = data.results[0];
      category.value = trivia.category;
      question.value = trivia.question;
      correctAnswer.value = trivia.correct_answer;
      incorrectAnswers.value = trivia.incorrect_answers;
      type.value = trivia.type;
      difficulty.value = trivia.difficulty;
      shuffledAnswers.value = shuffleArray([
        ...incorrectAnswers.value,
        correctAnswer.value,
      ]);
    });
};
</script>
