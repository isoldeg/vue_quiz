<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template slot="lead">
                <!-- currentQuestion is passed from parent component -->
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item 
                    v-for="(answer, index) in shuffledAnswers"
                    :key="index"
                    @click.prevent="selectAnswer(index)"
                    :class="answerClass(index)"
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
            <b-button @click="next" variant="success" href="#">
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
    import _ from 'lodash'

    /* regular exported object */
    export default {
        props: {
            /* anything passed from App.vue to QuestionBox.vue has to be referenced in props to be displayed in HTML */
            currentQuestion: Object, /* object data type received from the parent */
            next: Function,  /* next is a function from parent */
            increment: Function /* function to increment the count in Header */

        },
        data() {    /* variables */
            return {
                selectedIndex: null, /* initially set to null */
                correctIndex: null, /* store the correct answer's index */
                shuffledAnswers: [],
                answered: false /* check if the user answered (submitted) their answer */
            }
        },
        computed: {
            /* get the answers */
            answers() {
                /* use this to access props */
                let answers = [...this.currentQuestion.incorrect_answers]    /* get currentQuestion from props -> assign to new array */
                answers.push(this.currentQuestion.correct_answer)    /* push correct_answer to end of array */
                return answers
            }
        },
        watch: {
            /* same name as currentQuestion prop */
            currentQuestion: {
                immediate: true,    /* runs currentQuestion when it get updated but also when it first gets passes in props */
                handler() {
                    this.selectedIndex = null
                    this.answered = false   /* reset to false for every question */
                    this.shuffleAnswers()
                }
            }
        },
        methods: {
            selectAnswer(index) {
                this.selectedIndex = index
            },
            submitAnswer() {
                let isCorrect = false

                /* if you user selects the right answer */
                if (this.selectedIndex === this.correctIndex) {
                    isCorrect = true
                }

                this.answered = true    /* if answered the Submit but will be disabled */

                /* pass this to App to pass to Header to increment the count */
                this.increment(isCorrect)
            },
            shuffleAnswers() {
                let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]    /* get currentQuestion and correct_answer in array */
                this.shuffledAnswers = _.shuffle(answers)   /* shuffles the answers -> _ = lodash */
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer) /* index of correct answer */
            },
            answerClass(index) {
                let answerClass = ''
                
                if (!this.answered && this.selectedIndex === index) {
                    answerClass = 'selected'
                } else if (this.answered && this.correctIndex === index) {
                    answerClass = 'correct'
                } else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
                    answerClass = 'incorrect'
                }

                return answerClass

                /*
                [
                    !answered && selectedIndex === index ? 'selected' :
                    answered && correctIndex === index ? 'correct' :
                    answered && selectedIndex === index && correctIndex !== index ? 'incorrect' : ''
                ]
                */
            }
        }
    }
</script>

<!-- this is scoped so only affect QuestionBox component -->
<style scoped>
.list-group {
    margin-bottom: 15px;
}

/* when the user hovers over the item */
.list-group-item:hover {
    background: #EEE;
    cursor: pointer;
}

.btn {
    margin: 0 5px;
}

/* for a selected answer */
.selected {
    background-color: lightblue;
}

/* for the correct answer */
.correct {
    background-color: lightgreen;
}

/* for the incorrect answer */
.incorrect {
    background-color: red;
}
</style>