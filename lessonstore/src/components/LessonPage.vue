<template>
    <div>
        <!-- <div id = "controls">
            <br>
            <input class="searchLetter" placeholder="Type to search lessons" v-model="searchLetter" v-on:input="searchItem()">
            <br>
            <form>
                <input type="radio" id="topic" name="sort" value="topic" v-model="sortLessons.attribute">
                <label for="topic">topic</label><br>
                <input type="radio" id="location" name="sort" value="location" v-model="sortLessons.attribute">
                <label for="location">Location</label><br>
                <input type="radio" id="price" name="sort" value="price" v-model="sortLessons.attribute">
                <label for="price">Price</label><br>
                <input type="radio" id="avail" name="sort" value="availability" v-model="sortLessons.attribute">
                <label for="avail">Availability</label><br>
        
                <br>

                <input type="radio" id="asc" name="order" value="asc" v-model="sortLessons.order">
                <label for="huey">Ascending</label><br>
                <input type="radio" id="desc" name="order" value="desc" v-model="sortLessons.order">
                <label for="huey">Descending</label><br>
            </form>
        </div> -->

        <div id = "lessonList">
            <ul>
                <li v-bind:key="lesson._id" v-for="lesson in lesson"> 
                    <img v-bind:src="lesson.image"> 
                    <h1>{{lesson.topic}}</h1>
                    <p>location: {{lesson.location}}</p>
                    <p>Price: £{{lesson.price}}</p>
                    <p>Spaces: {{lesson.spaces}}</p>
                    <button @click='addLesson(lesson)' :disabled='!countSpace(lesson)'><span v-bind:class="lesson.icon"></span> Add Lesson</button>
                </li>
            </ul>
        </div>
    </div>
</template>

<script> 

export default {
    name: "LessonPage",
    props: ['lesson'],
    data() {
        return {};
    },
    methods: {
        addLesson(lesson){
            this.$emit('addItem',lesson)
        },
        countSpace(lesson){
            return lesson.spaces != 0
        }
    },
    watch: {

    }
}
</script>
