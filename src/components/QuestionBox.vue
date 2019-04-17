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
                v-for="(answer, index) in answers" 
                :key="index">
                    {{ answer }}
                </b-list-group-item>                            
            </b-list-group>

            <b-button variant="primary" href="#">Submit</b-button>
            <b-button variant="success" @click="nextQuestion">Next Question</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
// Need to pass it in is a prop to use it 
export default {
    props: {
        question: Object,
        nextQuestion: Function
    },
    computed: {
        answers() {
            // Why did ... fix the problem when it displayed
            // an array of answers before?
            let answers = [...this.question.incorrect_answers];
            answers.push(this.question.correct_answer);

            return answers;
        }
    }
}
</script>
