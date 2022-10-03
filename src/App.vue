<template>
  <div class="main">
    <div v-if="toggle == 0">
      <input type="text" placeholder="Ismingizni kiriting" v-model="name">
      <button v-show="name.length>0" @click="start()">Boshlash</button>
    </div>
    <div v-if="toggle == 1">
      {{test.question}}
      <label v-for="(variant,index) of test.incorrect_answers" :key="index">
        <input type="radio" name="answer" v-model="answer" :value="variant">
        {{variant}}
      </label>
      <button @click="next()" v-show="answer">Keyingisi</button>
    </div>
    <div v-if="toggle == 2">
      To'g'ri topgan javoblar soni: {{trueAnswers}} ta
      <button @click="clear()">Boshidan boshlash</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      toggle:0,
      answer:'',
      name:'',
      tests:[],
      count:0,
      test:{
        incorrect_answers:[]
      },
      trueAnswers:0
    }
  },
  methods: {
    clear(){
      this.count = 0
      this.name = ''
      this.trueAnswers = ''
      this.test = {
        incorrect_answers:[]
      }
      this.toggle = 0
    },
    next(){
      if (this.answer == this.test.correct_answer){
        this.trueAnswers++
      }
      this.answer = ''
      this.count++ 
      if (this.count < this.tests.length){
        this.test = this.tests[this.count]
        this.test.incorrect_answers.push(this.test.correct_answer)
        this.test.incorrect_answers = this.shuffle(this.test.incorrect_answers)
      } else {
        this.toggle = 2
      }
    },
    start(){
      axios.get('https://opentdb.com/api.php?amount=10&type=multiple')
      .then(res => {
        console.log(res.data.results)
        if (res.status == 200){
          this.tests = res.data.results 
          this.toggle = 1
          this.test = this.tests[this.count]
          this.test.incorrect_answers.push(this.test.correct_answer)
          this.test.incorrect_answers = this.shuffle(this.test.incorrect_answers)
        }
      })
    },
    shuffle(array) {
      let currentIndex = array.length,  randomIndex;
      while (currentIndex != 0) {
        randomIndex = Math.floor(Math.random() * currentIndex);
        currentIndex--;
        [array[currentIndex], array[randomIndex]] = [
          array[randomIndex], array[currentIndex]];
      }
      return array;
    }
  },
}
</script>

<style lang="scss">
  @import '@/app.scss';
</style>
