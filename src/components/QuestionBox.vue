<template>
    <div class="question-box-container">
        <b-jumbotron>

            <template slot="lead">
                <!-- Get question from question array -->
                {{ question.question }}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item 
                v-for="(answer, index) in shuffledAnswers" 
                :key="index"
                @click="selectAnswer(index)"
                :class="[selectedIndex === index ? 'selected' : '']">
                    {{ answer }}
                </b-list-group-item>                            
            </b-list-group>

            <b-button 
                variant="primary"
                @click="submitAnswer"
                :disabled="selectedIndex < 0">
                Submit
            </b-button>
            <b-button 
                variant="success" 
                @click="nextQuestion"
                >
                Next Question
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'

// Need to pass it in is a prop to use it 
export default {
    props: {
        question: Object,
        nextQuestion: Function,
        increment: Function
    },
    data() {
        return {
            selectedIndex: null,
            shuffledAnswers: []
        }
    },
    computed: {
        answers() {
            // Why did ... fix the problem when it displayed
            // an array of answers before?
            let answers = [...this.question.incorrect_answers];
            answers.push(this.question.correct_answer);

            return answers;
        }
    },
    watch: {
        question: {
            immediate: true,
            handler() {
                this.selectedIndex = null;
                this.shuffleAnswers();
            }
        }
    },
    methods: {
        selectAnswer(index) {
            this.selectedIndex = index;
        },
        submitAnswer() {
            let isCorrect = false;

            if (this.selectedIndex === this.correctIndex) {
                isCorrect = true;
            }

            this.increment(isCorrect);
        },
        shuffleAnswers() {
            let answers = [...this.question.incorrect_answers, this.question.correct_answer];
            this.shuffledAnswers = _.shuffle(answers);
            this.correctIndex = this.shuffledAnswers.indexOf(this.question.correct_answer);
        },
    }
}
</script>

<style scoped>
.list-group {
    margin-bottom: 15px;
}
.list-group-item:hover {
    background-color: #EEE;
    cursor: pointer;
}
.btn {
    margin: 0 5px;
}

.selected {
    background-color: lightgrey;
}

.correct {
    background-color: lightgreen;
}

.incorrect {
    background-color: lightcoral;
}
</style>
