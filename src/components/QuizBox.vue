<template>
  <div>
    <b-jumbotron>
      <template v-slot:header>Quiz Question</template>

      <template v-slot:lead>
        {{ currentQuestion.question}}
      </template>

      <hr>

      <b-list-group >
        <b-list-group-item
        v-for="(answer,index) in shuffledAnswers" :key="index" 
      @click.prevent="selectAnswer(index)" 
      :class= "answerClass(index)"
      
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button 
      variant="primary" 
      v-on:click= "submitAnswer"
      :disabled= "selectedIndex === null || answered"
      >
        Submit 
      </b-button><br><br>
      {{count === 11 ? 'end of quiz, thanks for playing!': 'number of questions left to answer '+ (11-count)}}
      
    </b-jumbotron>
  </div>
</template>

<script>

import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },

  data(){
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: null,
      answered: false,
      count: 1
    }
  },
  computed:{
    answers(){
      let answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
    }
  },

  watch: {
    currentQuestion: {
     immediate: true,
     handler() {
       this.selectedIndex = null
       this.answered = false
        this.shuffleAnswers()
     }       
    }
  },

  
  methods:{
    selectAnswer(index){
      this.selectedIndex = index
      // console.log(index)
    },
    submitAnswer(){
      let isCorrect= false
      if (this.selectedIndex === this.correctIndex ){
        isCorrect = true
      }

      setTimeout(()=>{
        this.next()
      },2000)

      this.answered = true

      this.increment(isCorrect)
      this.count++
    },
    shuffleAnswers(){
      let answers = [... this.currentQuestion.incorrect_answers,this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    answerClass(index){
      let answerClass = ''

      if(!this.answered && this.selectedIndex === index){
        answerClass = 'selected'
      }else if (this.answered && this.correctIndex === index){
        answerClass = 'correct'
      } else if(this.answered && 
      this.selectedIndex === index && 
      this.correctIndex !== index){
        answerClass = 'incorrect'
      }
      return answerClass
    }
  }
  
}
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}

.btn {
  margin: 0 15px;
}

.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}

.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color: lightgreen;
}

.incorrect {
  background-color: lightcoral;
}
</style>