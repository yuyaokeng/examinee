<template>
    <div class="newexam">
        <form
            class="form-horizontal border bg-light p-5"
            @submit.prevent="submit"
        >
            <div class="form-group">
                <label
                    for="title"
                    class="col-md-4 control-label"
                >Exam Title</label>

                <div class="col">
                    <input
                        id="title"
                        v-model="exam.title"
                        type="text"
                        class="form-control"
                        name="title"
                        required
                        autofocus
                    >
                </div>
            </div>

            <div class="form-group">
                <label
                    for="description"
                    class="col-md-4 control-label"
                >Description</label>
                 <div class="col">
                    <textarea
                        id="description"
                        v-model="exam.description"
                        name="description"
                        class="form-control"
                        rows="8"
                    />
                </div>
            </div>
            <div class="row">
                <div class="col-6">
                   <div class="form-group">
                        <label for="difficulty">Difficulty Level</label>
                        <select class="form-control" v-model="exam.difficulty" id="difficulty">
                            <option value="1">Easy</option>
                            <option value="2">Medium</option>
                            <option value="3">Hard</option>
                        </select>
                    </div>
                </div>
                
                <div class="col-6">
                    <div class="form-group">
                        <label for="pass_mark" class="control-label">Pass Mark</label>

                        
                            <input
                                id="pass_mark"
                                type="number"
                                v-model="exam.pass_mark"
                                class="form-control"
                                name="pass_mark"
                                min="0"
                            >
                        
                    </div>
                </div>
                <div class="col-6">
                    <div class="form-group">
                        <label for="duration" class="control-label">Duration</label>

                        
                            <input
                                id="duration"
                                type="number"
                                v-model="exam.duration"
                                class="form-control"
                                name="duration"
                                min="0"
                            >
                       
                    </div>
                </div>
                 <div class="col-6">
                    <div class="form-group">
                        <label for="question_number" class="control-label">Question Number</label>
                           <input
                                id="number_of_questions"
                                type="number"
                                v-model="exam.number_of_questions"
                                class="form-control"
                                name="number_of_questions"
                                min="0"
                            >
                            <div class="form-check">
                            <input class="form-check-input" v-model="exam.random_questions" type="checkbox" value="" id="random_question">
                            <label class="form-check-label" for="random_questions">
                                Random Questions
                            </label>
                            </div>
                       
                    </div>
                </div>
                <div class="col-6">
                   <div class="form-group">
                        <label for="status">Status</label>
                        <select class="form-control" id="status" v-model="exam.status">
                        <option value="1">Free</option>
                        <option value="2">Course</option>
                        <option value="3">Course &amp; Paid</option>
                        <option value="4">Paid</option>
                        
                        </select>
                    </div>
                </div>
                <div class="col-3" v-if="exam.status==3 || exam.status==4 ">
                    <div class="form-group">
                        <label for="price" class="control-label">Price</label>

                        
                            <input
                                id="price"
                                type="number"
                                v-model="exam.price"
                                class="form-control"
                                name="price"
                                min="0"
                            >
                       
                    </div>
                </div>
                
                <div class="col-3 mt-4">
                    <div class="form-check">
                        <input class="form-check-input" v-model="exam.certification" type="checkbox" value="" id="certification">
                        <label class="form-check-label" for="certification">
                            Certification
                        </label>
                    </div>
                </div>
            </div>
            <div class="form-group">
                        <Multiselect
                            id="ajax"
                            v-model="selectTopics"
                            label="title"
                            track-by="id"
                            placeholder="Type to search Topic"
                            open-direction="bottom"
                            :options="topics"
                            :multiple="true"
                            :searchable="true"
                            :loading="isLoading"
                            :internal-search="false"
                            :clear-on-select="true"
                            :close-on-select="false"
                            :max-height="600"
                            :show-no-results="true"
                            :taggable="true"
                            :hide-selected="true"
                            @tag="createTopic"
                            @search-change="loadTopics"
                        >
                            <template
                                slot="tag"
                                slot-scope="{ option, remove }"
                            >
                                <span class="custom__tag"><span>{{ option.title }}</span><span
                                    class="custom__remove"
                                    @click="remove(option)"
                                >x</span></span>
                            </template>

                            <template
                                slot="clear"
                                slot-scope="props"
                            >
                                <div
                                    v-if="selectTopics.length"
                                    class="multiselect__clear"
                                    @mousedown.prevent.stop="clearAll(props.search)"
                                />
                            </template>

                            <span slot="noResult">Oops! No elements found. Consider changing the search query.</span>
                        </Multiselect>
                    </div>

            <div class="form-group">
                <div class="col-md-8 col-md-offset-4">
                    <button
                        type="submit"
                        class="btn btn-primary"
                    >
                        {{ exam.id ? 'Update Exam': 'Create Exam' }}
                    </button>
                </div>
            </div>
        </form>
    </div>
</template>

<script>
import Multiselect from 'vue-multiselect'

export default {
    components:{
        Multiselect
    },
    props: {
        exam: {
            type: Object,
            default () {
                return {
                    title: '',
                    description: '',
                    status:1,
                    pass_mark:0,
                    duration:null,
                    number_of_questions:null,
                    random_questions:true,
                    difficulty:1,
                    price:0,
                    certification:false



                }
            }
        }
    },
    data () {
        return {
            topics: [],
            selectTopics: [],
            isLoading: false,
            topicTimeout: null

        }
    },
    computed: {

    },
    created () {

    },
    methods: {
        submit () {
            const params = {
                title: this.exam.title,
                description: this.exam.description,
                status:this.exam.status,
                pass_mark:this.exam.pass_mark,
                duration:this.exam.duration,
                number_of_questions:this.exam.number_of_questions,
                random_questions:this.exam.random_questions,
                difficulty:this.exam.difficulty,
                certification:this.exam.certification,
                price:this.exam.price,
                course_id:this.$route.params.id ? this.$route.params.id:null
            }

            if (!this.exam.id) {
                axios.post(`/api/exams`, params)
                    .then(res => {
                        this.$emit('save', res.data.data)
                        this.exam.title = ''
                        this.exam.description = ''
                    })
            } else {
                axios.put(`/api/exams/${this.exam.id}`, params)
                    .then(res => {
                        this.$emit('update', res.data.data)
                    })
            }
        },
        loadTopics (search) {
            const params = {
                s: search
            }

            if (this.topicTimeout) {
                clearInterval(this.topicTimeout)
            }

            this.isLoading = true

            this.topicTimeout = setTimeout(() => {
                axios.get(`/api/topics`, { params })
                    .then(res => {
                        this.topics = res.data.data
                        this.isLoading = false
                    })
            }, 300)
        },
        clearAll () {
            this.selectTopics = []
        },
        createTopic (searchQuery, id) {
            const params = {
                title: searchQuery
            }

            axios.post(`/api/topics`, params)
                .then(res => {
                    this.selectTopics.push(res.data.data)
                })
        }
    }
}
</script>
