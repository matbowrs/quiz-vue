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
                :class="[answerClass(index)]"
                >
                    {{ answer }}
                </b-list-group-item>                            
            </b-list-group>

            <!-- Submit and Next button will be hidden at the end of the
                quiz. Only the Play Again button will appear at the end. 
                Hence, the v-if statements.
            -->
            <b-button 
                variant="primary"
                v-if="numAnswered != questions.length"
                @click="submitAnswer"
                :disabled="selectedIndex === null || answered">
                Submit
            </b-button>
            <b-button 
                variant="success" 
                v-if="numAnswered != questions.length"
                @click="nextQuestion"
                :disabled="answered === false"
                >
                Next Question
            </b-button>

            <!-- Only appears at the end -->
            <br />
            <p v-if="numAnswered === questions.length">
                <strong>You got {{ numCorrect }} / {{ numTotal }} correct!</strong>
            </p>

            <br />
            <b-button
                variant="success"
                v-if="numAnswered === questions.length "
                @click='reloadQuestions'
                >
                Play Again!
            </b-button>

        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'

// Need to pass variables from other .vue files in is a prop to use it 
export default {
    props: {
        question: Object,
        nextQuestion: Function,
        increment: Function,
        questions: Array,
        numTotal: Number,
        numCorrect: Number
    },
    data() {
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false,
            numAnswered: null
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
                this.answered = false;
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

            this.answered = true;
            this.numAnswered++;
            this.increment(isCorrect);
        },
        shuffleAnswers() {
            let answers = [...this.question.incorrect_answers, this.question.correct_answer];
            this.shuffledAnswers = _.shuffle(answers);
            this.correctIndex = this.shuffledAnswers.indexOf(this.question.correct_answer);
        },
        // Based on what the user choose, apply a CSS class to their choice 
        answerClass(index) {
            let answerClass = '';
            if (!this.answered && this.selectedIndex === index) {
                answerClass = 'selected';
            } else if (this.answered && this.correctIndex === index) {
                answerClass = 'correct';
            } else if (this.answered && this.selectedIndex === index 
                        && this.correctIndex !== index) {
                answerClass = 'incorrect';
            } else
                answerClass = '';  
            
            return answerClass;
        },
        // At the end if the user wants to play again, this reloads the page
        reloadQuestions() {
            location.reload();
        }
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
