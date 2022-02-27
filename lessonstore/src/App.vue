<template>
  <div id="app">
    <lesson-component :lesson="lesson" @addItem="addItem"></lesson-component>
  </div>
</template>

<script>
// import CheckoutComponent from './components/CheckoutPage.vue'
import LessonComponent from './components/LessonPage.vue'

export default {
  name: 'App',
  components: {
    LessonComponent
  },
  data() {
    return {
      lesson: [],
      searchLessons: [],
      showCart: false,
      showCheckout: false,
      orderComplete: false,
      searchLetter: '',
      cart: [],
      order: {
        firstName: '',
        phoneNo: ''
      },
      sortLessons: {
        attribute: 'topic',
        order: 'asc'
      }
    }
  },
  methods:{
    addItem(lesson){
      if(lesson.spaces > 0){
        lesson.spaces -= 1
        this.cart.push(lesson._id) //pushes id to the cart

        for(let i = 0; i < this.lesson.length; i++){
          if(lesson._id === this.lesson[i]._id){
            this.lesson[i].spaces = lesson.spaces;
          }
        }
      }
    },
    searchItem(){
      let vm = this.searchLessons;

      if(this.searchLetter != ''){ //checks if the user has entered search information.
        fetch('https://cst3145-node-server.herokuapp.com/search/collection/lesson/' + this.searchLetter).then(
        function (response) {
          response.json().then(
            function (json) {
              console.log(json)

              // REMOVED app. at the start
              vm.searchLessons = json;
            });
        });
      }
    },
    removeItem(id){
      for(let i = 0; i < this.cart.length; i++){
        if(id === this.cart[i]){ //searches for the id that is in the cart to get the array index
          this.cart.splice(i, 1); //removes the item from the cart using array index
          break; //stops for loop
        }
      }
      for(let i = 0; i < this.lesson.length; i++){
        if(id === this.lesson[i]._id){
          this.lesson[i].spaces += 1
        }
      }
    },
    placeOrder(){
      let subjectSpace = [];
      let uniqueIDs = [];

      let vm = this.lesson;


      //stack the same lesson and increment count of it within one order
      this.cart.forEach(function(cartItem){
        vm.forEach(function(lessonItem){
          if(cartItem === lessonItem._id && uniqueIDs.includes(cartItem) == false ){
            let temp = lessonItem.topic + "-" + lessonItem.location
            subjectSpace.push({Lesson: temp,Ordered: 0})
            uniqueIDs.push(cartItem);
          }
        });
      });

      this.cart.forEach(function(cartItem){
        vm.forEach(function(lessonItem){
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

      const order = {name: this.order.firstName, phoneNumber: this.order.phoneNo, lessonID: uniqueIDs, orderedSpaces: subjectSpace};

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
      this.cart = [];
      this.searchLetter = '';

      fetch('https://cst3145-node-server.herokuapp.com/collection/lesson/').then(
        function (response) {
          response.json().then(
            function (json) {
              console.log(json)
              this.searchLessons = json;
            });
        });
    },
    openCheckout(){
      this.showCheckout = this.showCheckout ? false : true;
    },
    getLessons(){
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
  created: function(){
    fetch('https://cst3145-node-server.herokuapp.com/collection/lesson')
    .then(response => response.json())
    .then(data => (this.lesson = data))
  },
  computed: {
    // countCart(){ //counts the total items in the cart
    //   if (this.cart.length > 0){
    //     this.showCart = true;
    //   } else {
    //     this.showCart = false; //disables the showCart button
    //     this.showCheckout = false; //disables the checkout
    //   }
    //   return this.cart.length;
    // },
    // sorting(){
    //   let data = this.lesson.slice();

    //   if(this.searchLetter != ''){ //checks if the user has entered search information.
    //     data = this.searchLessons.slice()

    //     for (let i = 0; i < this.searchLessons.length; i++) {
    //       for(let y = 0; y < this.lesson.length; y++){
    //         if(this.searchLessons[i]._id === this.lesson[y]._id){
    //           console.log("MATCH LESSONS " + y + this.lesson[y].topic)
    //           this.searchLessons[i].spaces = this.lesson[y].spaces
    //           console.log(this.searchLessons[i].spaces);
    //         }
    //       }
    //     }
    //   }
      
    //   //sort the array based on the attribute selected
    //   if(this.sortLessons.attribute == "topic"){
    //     data.sort(comparetopic);
    //   } else if(this.sortLessons.attribute == "location"){
    //     data.sort(comparelocation);
    //   } else if(this.sortLessons.attribute == "price"){
    //     data.sort(comparePrice); 
    //   } else if(this.sortLessons.attribute == "availability"){
    //     data.sort(compareSpaces); 
    //   } else {
    //     data.reverse();
    //   }

    //   if(this.sortLessons.order == "asc"){
    //     return data
    //   } else {
    //     return data.reverse(); //reverse the array if the option to sort descending is selected
    //   }

    //   function comparetopic(a, b){
    //     let x = a.topic.toLowerCase();
    //     let y = b.topic.toLowerCase();
    //     if (x < y) {return -1;}
    //     if (x > y) {return 1;}
    //     return 0;
    //   }

    //   function comparelocation(a, b){
    //     let x = a.location.toLowerCase();
    //     let y = b.location.toLowerCase();
    //     if (x < y) {return -1;}
    //     if (x > y) {return 1;}
    //     return 0;
    //   }

    //   function comparePrice(a, b,){
    //     if(a.price > b.price) return 1;
    //     if(a.price < b.price) return -1;
    //     return 0;
    //   }

    //   function compareSpaces(a, b,){
    //     if(a.spaces > b.spaces) return 1;
    //     if(a.spaces < b.spaces) return -1;
    //     return 0;
    //   }
    // },
    verifyDetails(){
      let onlyLetters = /^[a-zA-Z]+$/ //regular expressions for checking account details
      let onlyNumbers = /^[0-9]*$/

      if(this.order.phoneNo.length == 11 && onlyNumbers.test(this.order.phoneNo) == true && onlyLetters.test(this.order.firstName) == true){ //checks if all the data matches regex
        return true;
      } else
      return false;
    },
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0px;
}
</style>
