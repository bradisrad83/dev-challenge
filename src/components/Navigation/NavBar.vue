<template lang="">
    <div>
        <div class="w-full py-3 px-8 flex items-center justify-start shadow text-gray-500 border-b-2 border-gray-200">
            <div @click="toggleMenu()" class="mr-6 cursor-pointer">
                <menu-icon/>
            </div>
            <p class="text-lg">KelbyOne API Demo</p>
        </div>
        <div class="flex">
            <div v-if="showMenu" class="z-40 w-3/4 md:w-1/6 h-screen px-4 py-4 border-r-2 border-gray-200 gap-2">
                <div class="mb-4">
                    <h2 class="text-xl font-bold text-gray-500">Results Per Page</h2>
                    <select v-model="resultsPerPage" class="rounded-lg border-2 border-gray-200 px-4 py-1">
                        <option value="10">10</option>
                        <option value="20">20</option>
                        <option value="40">40</option>
                        <option value="60">60</option>
                        <option value="100">100</option>
                    </select>
                </div>

                <h2 class="text-xl font-bold text-gray-500">Categories</h2>
                <div v-for="category in categories" :key="category.id" class="flex items-center">
                    <label class="text-gray-500 block" >
                        <span @click.prevent="setCheckbox(category.id)">
                            <input type="checkbox" :value="category.id" v-model="selectedCategories" class="mr-1">
                        </span>
                        <span v-html="category.title"></span>
                    </label>
                </div>
            </div>
            <div class="px-4 py-8 w-full bg-white grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4 w-full">
                <div class="col-span-full flex justify-between text-gray-600 mb-4">
                    <div class="flex items-center cursor-pointer" :class="currentPage == 1 ? 'opacity-50': ''" @click="previousPage()">
                        <arrow-left />
                        <p class="ml-1 font-bold">Previous Page</p>
                    </div>
                    <div class="flex items-center cursor-pointer" @click="nextPage()">
                        <p class="mr-1 font-bold">Next Page</p>
                        <arrow-right />
                    </div>
                </div>
                <course v-for="course in courses" :key="course.id" :course="course" />
            </div>
        </div>
    </div>
</template>
<script>
import MenuIcon from 'vue-material-design-icons/Menu.vue';
import MenuLeftIcon from 'vue-material-design-icons/MenuLeft.vue';
import MenuRightIcon from 'vue-material-design-icons/MenuRight.vue';
import ArrowLeft from 'vue-material-design-icons/ArrowLeft.vue'
import ArrowRight from 'vue-material-design-icons/ArrowRight.vue'
import Course from '../Courses/Course.vue';
import axios from 'axios';
export default {
    name: 'NavBar',
    components: { MenuIcon, MenuLeftIcon, MenuRightIcon, ArrowRight, ArrowLeft, Course },
    data() {
        return {
            showMenu: false,
            courses: [],
            categories: [],
            lessons: [],
            selectedCategories: [],
            categoryCourses: [],
            currentPage: 1,
            resultsPerPage: 10,
            baseUrl: 'https://kelbystaging.wpengine.com/wp-json/ko/v4/'
        }
    },
    mounted() {
        this.getCourses();
        this.getCategories();
        this.getLessons();
    },
    methods: {
        toggleMenu() {
            this.showMenu = !this.showMenu;
        },
        setCheckbox(id) {
            console.log(id);
            if(this.selectedCategories.includes(id)) {
                this.selectedCategories = [];
            } else {
                this.selectedCategories = [id];
                this.currentPage = 1;
                this.getCategoryCourses();
            }
        },  
        previousPage() {
            if(this.currentPage > 1) {
                this.currentPage--;
            }
        },  
        nextPage() {
            this.currentPage++;
        },  
        async getCourses() {
           this.courses = await axios.get(this.baseUrl+'courses/?page='+this.currentPage+'&per_page='+this.resultsPerPage).then(function(response) {
                return response.data.data;
            }).catch(function(error) {
                console.log(error);
            })
        },
        async getCategoryCourses() {
            this.courses = await axios.get(this.baseUrl+'categories/'+this.selectedCategories[0]+'/courses?page='+this.currentPage+'&per_page='+this.resultsPerPage).then(function(response) {
                return response.data.data;
            }).catch(function(error) {
                console.log(error);
            })
        },
        async getCategories() {
            this.categories = await axios.get(this.baseUrl+'categories').then(function(response) {
                return response.data.data;
            }).catch(function(error) {
                console.log(error);
            })
        },
        async getLessons() {
            this.lessons = await axios.get(this.baseUrl+'lessons').then(function(response) {
                return response.data.data;
            }).catch(function(error) {
                console.log(error);
            })
        },      
    },
    watch: {
        resultsPerPage(newValue, oldValue) {
            if(newValue !== oldValue) { 
                if(this.selectedCategories.length) {
                    this.getCategoryCourses();
                } else {
                    this.getCourses();
                }
            }
        },
        currentPage(newValue, oldValue) {
            if(newValue !== oldValue) {
                if(this.selectedCategories.length) {
                    this.getCategoryCourses();
                } else {
                    this.getCourses();
                }
            }
        },
        selectedCategories(newValue, oldValue) {
            if(newValue.length) {
               this.getCategoryCourses();
            } else {
                this.getCourses();
            }
        }
    }  
}
</script>
<style lang="">
    
</style>