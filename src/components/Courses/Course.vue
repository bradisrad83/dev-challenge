<template lang="">
    <div class="rounded-lg shadow-lg flex flex-col justify-between items-start bg-gray-100 rounded-lg">
        <div>
            <img :src="course.images.full" class="object-contain ronded-lg"/>
            <h2 class="p-2 text-lg text-left font-bold text-gray-600 mt-2">{{course.title}}</h2>
        </div>
        <div class="p-2">
            <p class="text-gray-500">
                Instructor:<span class="font-bold ml-2">{{ instructor }}</span>
            </p>
            <p class="text-gray-500">
                Created:<span class="font-bold ml-2">{{ getDate }}</span>
            </p>
        </div>
    </div>
</template>
<script>
import axios from 'axios';
export default {
    props: {
        course: {
            type: Object,
            required: true
        },
    }, 
    data() {
        return {
            instructor: '',
            baseUrl: 'https://kelbystaging.wpengine.com/wp-json/ko/v4/',
             months: ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"],
        }
    },
    computed: {
        getDate() {
            let month = new Date(this.course.dates.created).getMonth();
            let year = new Date(this.course.dates.created).getFullYear();
            return this.months[month]+' '+year;
        }
    },  
    methods: {
        async getInstructor() {
            this.instructor = await axios.get(this.baseUrl+'instructors/'+this.course.instructors[0]).then(function(response) {
                return response.data.data.title;
            }).catch(function(error) {
                console.log(error);
            })
        }
    },
    mounted() {
        this.getInstructor();
    }
}
</script>
<style lang="">
    
</style>