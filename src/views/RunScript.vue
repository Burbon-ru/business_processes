<template>
    <div class="run_script">
        <question-answers
            v-for="question in questionsInMessages"
            :key="question.id"
            :question="question"
            :number="question.id"
            :currentQuestion="currentQuestion"
            @change-step="changeStep"
        />
    </div>
</template>

<script>
    import {mapActions, mapGetters} from 'vuex';
    import QuestionAnswers from '@/components/RunScript/questionAnswers.vue';

    export default {
        name: "RunScript",
        data: () => ({
            currentQuestion: 0,
            questionsInMessages: []
        }),
        computed: {
            ...mapGetters([
                'questionsInCurrentScript'
            ])
        },
        components: {
            QuestionAnswers
        },
        watch: {
            questionsInCurrentScript (val) {
                let key = this.getKeyCurrentQuestionInQuestionsInCurrentScriptArray();

                this.currentQuestion = val[key].id;
                this.questionsInMessages.push(val[key]);
            }
        },
        mounted () {
            this.$store.dispatch('setCurrentScriptId', this.$route.params.id);
            this.$store.dispatch('setQuestionsInCurrentScript');
        },
        methods: {
            ...mapActions([
                'getScriptById',
                'getQuestionById',
                'setCurrentScriptId',
                'setQuestionsInCurrentScript'
            ]),
            changeStep (next) {
                this.currentQuestion = next;

                if (typeof next == 'undefined') {
                    return;
                }

                let key = this.getKeyCurrentQuestionInQuestionsInCurrentScriptArray();
                this.questionsInMessages.push(this.questionsInCurrentScript[key]);
            },
            getKeyCurrentQuestionInQuestionsInCurrentScriptArray () {
                let key = 0;

                for (let questionKey in this.questionsInCurrentScript) {
                    if (this.questionsInCurrentScript[questionKey].id == this.currentQuestion) {
                        key = questionKey;
                    }
                }

                return key;
            }
        }
    }
</script>
