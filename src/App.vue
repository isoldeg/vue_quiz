<template>
  <div id="app">
    <Header 
      :numCorrect="numCorrect"
      :numTotal="numTotal"
    />

    <b-container class="bv-example-row">
      <b-row>
        <b-col sm="6" offset="3">
          <!-- can pass methods and data variables to child components by using v-bind with custom attributes -->
          <!-- pass the questions, the next method -->
          <QuestionBox 
            v-if="questions.length"
            :currentQuestion="questions[index]"
            :next="next"
            :increment="increment"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import QuestionBox from './components/QuestionBox.vue'

export default {
  name: 'app',
  components: {
    Header,
    QuestionBox
  },
  data() {
    return {
      questions: [],
      index: 0,  /* keep an index of the question here */
      numCorrect: 0, /* number of correct answers */
      numTotal: 0 /* number of total questions */
    }
  },
  methods: {
    /* method to increment the index value */
    next() {
      this.index++
    },
    increment(isCorrect) {
      /* check if it is correct */
      if (isCorrect) {
        this.numCorrect++ 
      }
      this.numTotal++
    }
  },
  mounted: function() {
    /* fetch questions API, and pass in an object with the method you want to get from this */
    fetch('https://opentdb.com/api.php?amount=10&category=27&type=multiple', {
      method: 'get'
    })
      /* take the response */
      .then((response) => {
        return response.json()  /* the reponse in json format */
      })
      /* here to access data for the application */
      .then((jsonData) => {
        /* let the questions array equal what we get back from the API */
        this.questions = jsonData.results
      })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
