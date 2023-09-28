<template>
  <div id="main">
    <div v-if="!quizCompleted">
      <h2>Quiz</h2>
      <div id="quiz-body">
        <div id="quiz-question">
          <h3>Question No. {{ currentQuestion + 1 }}</h3>
          <h4>{{ getCurrentQuestion.question }}</h4>
        </div>
        <div>
          <span v-for="(option, id) in getCurrentQuestion.choices" :key="id" :id:Number="id" :value="option" :class="`option ${questionAnswered !== false ? 'disabled' : ''} ${questionAnswered === true ? getCurrentQuestion.answer !== option
              ? 'wrong'
              : 'correct'
              : ''
            }`" @click="setAnswer(option)">
            {{ option }}
          </span>
        </div>
        <button type="submit" @click="nextQuestion" :disabled="!questionAnswered" @keydown.enter="nextQuestion()">
          {{ currentQuestion !== questions.length - 1 ? 'Next' : 'Done' }}
        </button>
      </div>
    </div>
    <div v-else id="quiz-result">
      <h2>You've finished the quiz!</h2>
      <h3>Your score is {{ score }} / {{ questions.length }}.</h3>
      <button @click="resetQuiz" id="btn-retake">Retake Quiz</button>
    </div>
  </div>
</template>
<script lang="ts">
import { defineComponent, ref, computed } from 'vue'

export default defineComponent({
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Index',
  setup() {
    const questions = ref([
      {
        id: 1,
        question: 'What is a cat?',
        answer: 'animal',
        choices: ['human', 'thing', 'animal', 'place'],
        selected: null
      },
      {
        id: 2,
        question: 'What is a bag?',
        answer: 'thing',
        choices: ['human', 'thing', 'animal', 'place'],
        selected: null
      },
      {
        id: 3,
        question: 'Kanji for good is...?',
        answer: '良',
        choices: ['長', '昔', '深', '良'],
        selected: null
      },
      {
        id: 4,
        question: 'What is Japanese word for "to climb"?',
        answer: '登る',
        choices: ['登る', '食べる', '寝る', '愛する'],
        selected: null
      },
      {
        id: 5,
        question: 'What is 阿呆 in English?',
        answer: 'idiot',
        choices: ['pretty', 'new', 'thin', 'idiot'],
        selected: null
      }
    ])

    const quizCompleted = ref(false)
    const currentQuestion = ref(0)
    const score = ref(0)
    const questionAnswered = ref(false)
    // const getCurrentQuestion = ref(questions.value[currentQuestion.value])
    // const score = computed(() => {
    //   let value = 0
    //   questions.value.map((question) => {
    //     if (question.answer === question.selected) {
    //       value++
    //     }
    //   })
    //   return value
    // })

    const getCurrentQuestion = computed(() => {
      let question = questions.value[currentQuestion.value]

      function randomize(arr: any) { // Find if randomize method below can be used here to avoid duplication 
        let temp = [...arr] // Copy array
        let newArr: any = []

        function changeArr(this: any) {
          let item = temp[Math.floor(Math.random() * temp.length)] // Get random item
          let itemIndex = temp.indexOf(item) // Get the index of the random item
          newArr.push(item)
          temp.splice(itemIndex, 1) // Remove the item from the array

          if (temp.length !== 0) {
            changeArr()
          } else {
            console.log(`New array ${newArr}`)
            // this.questions = newArr
          }
        }

        changeArr()
        return newArr
      }

      question.choices = randomize(question.choices)
      return question
    })

    // const setAnswer = e => {
    //   questions.value[currentQuestion.value].selected = e.target.value
    //   getCurrentQuestion.value.selected = e.target.value

    //   console.log('asfdjhgfdsa')
    //   e.target.value = null
    //   getCurrentQuestion.value.selected = null
    // }

    // const nextQuestion = () => {
    //   if (currentQuestion.value < questions.value.length - 1) {
    //     currentQuestion.value++
    //   } else {
    //     quizCompleted.value = true
    //   }
    // }

    return {
      questions,
      score,
      questionAnswered,
      currentQuestion,
      quizCompleted,
      getCurrentQuestion
    }
  },
  methods: {
    getScore(id: Number, guess: any) {
      let question = this.questions[id]

      if (question.answer === guess) {
        this.score++
      }

      console.log(this.score)
    },
    setAnswer(option: any) {
      let selected = option
      if (this.questionAnswered != true) {
        this.questions[this.currentQuestion].selected = selected
        this.getCurrentQuestion.selected = selected

        console.log(selected)
        this.getCurrentQuestion.selected = null

        this.getScore(this.currentQuestion, selected)
        this.questionAnswered = true
      }
    },
    nextQuestion() {
      if (this.currentQuestion < this.questions.length - 1) {
        this.currentQuestion++
        this.questionAnswered = false
      } else {
        this.quizCompleted = true
      }
    },
    resetQuiz() {
      this.questions = this.randomize(this.questions)
      this.quizCompleted = false
      this.currentQuestion = 0
      this.score = 0
      this.questionAnswered = false
    },
    randomize(arr: any) {
      let temp = [...arr]
      let newArr: any = []

      function changeArr(this: any) {
        let item = temp[Math.floor(Math.random() * temp.length)] // Get random item
        let itemIndex = temp.indexOf(item) // Get the index of the random item
        newArr.push(item)
        temp.splice(itemIndex, 1) // Remove the item from the array

        if (temp.length !== 0) {
          changeArr()
        } else {
          console.log(`New array ${newArr}`)
        }
      }

      changeArr()
      return newArr
    }
  },
  beforeMount() {
    this.questions = this.randomize(this.questions)
  }
})
</script>
<style scoped>
#main {
  margin: 4% 30%;
  font-size: large;
}

#quiz-body,
#quiz-result {
  display: flex;
  flex-direction: column;
  color: black;
  background-color: lightgray;
  border: 1px solid #494343;
  padding: 10px;
  border-radius: 2.5px;
}

#quiz-question {
  margin-left: 10px;
}

#quiz-result {
  margin-top: 50%;
  text-align: center;
}

.option {
  display: grid;
  text-align: center;
  color: black;
  background-color: white;
  margin: 10px;
  padding: 20px;
  border: 2px solid #464343;
  border-radius: 3px;
  cursor: pointer;
}

.option:active {
  background-color: #2fa72f;
  border: 2px solid #2fa72f;
}


.disabled {
  cursor: initial;
}

.correct {
  background-color: lightgreen;
  border: 2px solid #2fa72f;
  transition: background-color 0.5s ease;
}

.wrong {
  background-color: #f1adad;
  border: 2px solid #943b3b;
  transition: background-color 0.5s ease;
}

button {
  cursor: pointer;
  margin: 10px 30px;
  padding: 10px;
  background-color: lightseagreen;
  border: 1px solid #0f635e;
  border-radius: 4px;
  font-size: large;
}

#btn-retake {
  background-color: lightsalmon;
  border: 1px solid #da7248;
}

@media (max-width:450px) {
  #main {
    left: 0;
    top: 20;
    margin: 4% 10%;
    font-size: small;
  }
}
</style>
