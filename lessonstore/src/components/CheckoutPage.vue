<template>
<div>
    <div id = "controls">
        <p>Please enter your details</p>
        <label for="fname">Name</label><br>
        <input v-model="firstName"><br>
        <label for="avail">Phone Number</label><br>
        <input v-model="phoneNo"><br>
        <button @click='placeOrder()' :disabled='!verifyDetails()'>Place Order</button>
    </div>

    <ul>
        <li v-bind:key="lesson.key" v-for="lesson in getLessons()"> 
            <img v-bind:src="lesson.image"> 
            <h1>{{lesson.topic}}</h1>
            <p>location: {{lesson.location}}</p>
            <p>Price: £{{lesson.price}}</p>
            <button @click='removeLesson(lesson._id)'><span v-bind:class="lesson.icon"></span> Remove Lesson</button>
        </li>
    </ul>

</div>
</template>

<script>
export default {
    name: "CheckoutPage",
    props: ['cart','lesson'],
    data() {
        return {
            firstName: '',
            phoneNo: ''
        };
    },
    methods: {
        removeLesson(lessonid){
            this.$emit('removeItem',lessonid)
        },
        verifyDetails(){
            let onlyLetters = /^[a-zA-Z]+$/ //regular expressions for checking account details
            let onlyNumbers = /^[0-9]*$/

            if(this.phoneNo.length == 11 && onlyNumbers.test(this.phoneNo) == true && onlyLetters.test(this.firstName) == true){ //checks if all the data matches regex
              return true;
            } else
            return false;
          },
        getLessons(){
            //compares IDs in the cart against lessons in array of lessons
            let lessonDetails = []
            for (let id of this.cart){
              for (let _lesson of this.lesson){
                if(id === _lesson._id){
                  lessonDetails.push(_lesson)
                }
              }
            }
            return lessonDetails;
        },
    },
}
</script>
