<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">
        {{ currentQuestion.question }}
      </template>
      <hr class="my-4" />
      
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click.prevent="selectAnswer(index)"
          :class="answeredClass(index)"
          >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
        >
        Submit
      </b-button>

      <b-button
        @click="next"
        variant="success"
        :disabled="hasSubmitted === false"
        >
        Next
      </b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash';

export default {
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
      hasSubmitted: false
    }
  },
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true, // Runs the first time "currentQuestion" gets passed as props
      handler() { // Runs "currentQuestion" every time after the first time
        this.selectedIndex = null;
        this.answered = false;
        this.hasSubmitted = false;
        this.shuffleAnswers();
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
    },
    submitAnswer() {
      let isCorrect = false;
      if(this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
      this.hasSubmitted = true;
    },
    answeredClass(index) {
      let answered = this.answered;
      let selectedIndex = this.selectedIndex;
      let correctIndex = this.correctIndex;

      return !answered && selectedIndex === index ? 'selected'
            : answered && correctIndex === index ? 'correct'
            : answered && selectedIndex === index && correctIndex !== index ? 'incorrect'
            : '';
    }
  }
}
</script>

<style scoped>
  .list-group {
    margin: 15px;
  }
  .list-group-item:hover {
    background-color: #EEE;
    cursor: pointer;
  }

  .btn {
    margin: 0 5px;
  }

  .selected {
    background-color: lightblue;
  }

  .correct {
    background-color: green;
  }
  #correct:hover {
    background-color: green;
  }

  .incorrect {
    background-color: red;
  }
</style>