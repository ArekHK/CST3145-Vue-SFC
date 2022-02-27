<template>
    <div>
        <div id = "controls">
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
        </div>

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
        return {
            searchLetter: '',
            sortedLessons: [],
            sortLessons: {
                attribute: 'topic',
                order: 'asc'
            }
        };
    },
    methods: {
        addLesson(lesson){
            this.$emit('addItem',lesson)
        },
        countSpace(lesson){
            return lesson.spaces != 0
        },
        sorting(){
            this.sortedLessons = this.lesson;

            let data = this.sortedLessons;

            if(this.searchLetter != ''){ //checks if the user has entered search information.
              data = this.searchLessons.splice()

              for (let i = 0; i < this.searchLessons.length; i++) {
                for(let y = 0; y < this.lesson.length; y++){
                  if(this.searchLessons[i]._id === this.lesson[y]._id){
                    console.log("MATCH LESSONS " + y + this.lesson[y].topic)
                    this.searchLessons[i].spaces = this.lesson[y].spaces
                    console.log(this.searchLessons[i].spaces);
                  }
                }
              }
            }
            
            //sort the array based on the attribute selected
            if(this.sortLessons.attribute == "topic"){
              data.sort(comparetopic);
            } else if(this.sortLessons.attribute == "location"){
              data.sort(comparelocation);
            } else if(this.sortLessons.attribute == "price"){
              data.sort(comparePrice); 
            } else if(this.sortLessons.attribute == "availability"){
              data.sort(compareSpaces); 
            } else {
              data.reverse();
            }

            if(this.sortLessons.order == "asc"){
              this.sortedLessons = data
            } else {
              this.sortedLessons = data.reverse(); //reverse the array if the option to sort descending is selected
            }

            function comparetopic(a, b){
              let x = a.topic.toLowerCase();
              let y = b.topic.toLowerCase();
              if (x < y) {return -1;}
              if (x > y) {return 1;}
              return 0;
            }

            function comparelocation(a, b){
              let x = a.location.toLowerCase();
              let y = b.location.toLowerCase();
              if (x < y) {return -1;}
              if (x > y) {return 1;}
              return 0;
            }

            function comparePrice(a, b,){
              if(a.price > b.price) return 1;
              if(a.price < b.price) return -1;
              return 0;
            }

            function compareSpaces(a, b,){
              if(a.spaces > b.spaces) return 1;
              if(a.spaces < b.spaces) return -1;
              return 0;
            }
        }
    },
    watch: {
        lesson: {
            handler: function(){
                this.sorting()
            }
        }
    }
}
</script>
