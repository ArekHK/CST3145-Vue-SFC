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
        placeOrder(){
            let subjectSpace = [];
            let uniqueIDs = [];

            let lessonData = JSON.parse(JSON.stringify(this.lesson))

            //stack the same lesson and increment count of it within one order
            this.cart.forEach(function(cartItem){
                lessonData.forEach(function(lessonItem){
                if(cartItem === lessonItem._id && uniqueIDs.includes(cartItem) == false ){
                    let temp = lessonItem.topic + "-" + lessonItem.location
                    subjectSpace.push({Lesson: temp,Ordered: 0})
                    uniqueIDs.push(cartItem);
                }
                });
            });

            this.cart.forEach(function(cartItem){
                lessonData.forEach(function(lessonItem){
                if(cartItem === lessonItem._id){
                    let temp = lessonItem.topic + "-" + lessonItem.location
                    for(let i = 0; i < subjectSpace.length; i++){
                        if(temp == subjectSpace[i].Lesson){
                            subjectSpace[i].Ordered +=1;
                        }
                    }
                }
                });
            });

            console.log(subjectSpace);
            console.log(uniqueIDs);

            const order = {name: this.firstName, phoneNumber: this.phoneNo, lessonID: uniqueIDs, orderedSpaces: subjectSpace};

            fetch('https://cst3145-node-server.herokuapp.com/collection/order', { 
                method: 'POST',
                headers: {
                'Content-Type': 'application/json',
                },
                body: JSON.stringify(order),
            })
            .then(response => response.json())
            .then(responseJSON => {
                console.log('Success:', responseJSON);
            });

            for (let i = 0; i < this.cart.length; i++) {
                for(let y = 0; y < this.lesson.length; y++){
                if(this.cart[i] === this.lesson[y]._id){
                    let updateSpaces = { "spaces": (this.lesson[y].spaces) };
                    fetch("https://cst3145-node-server.herokuapp.com/collection/lesson/" + this.cart[i], {
                    method: "PUT", // sets method as PUT for HTTP
                    headers: {
                        "Content-Type": "application/json", //set data type as JSON
                    },
                    body: JSON.stringify(updateSpaces), //stringify the JSON object
                    });
                }
                }
            }
            
            alert("Thank you for your purchase :)");
            this.$emit('clearCart')

            fetch('https://cst3145-node-server.herokuapp.com/collection/lesson/')
            .then(response => response.json())
            .then(data => (this.searchLessons = data))
        },
    },
}
</script>
