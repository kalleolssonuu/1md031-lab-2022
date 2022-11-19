<template>
  <div>
  <div id="header">
        <img src="https://wallpaperaccess.com/full/1373403.jpg" alt="span" style="width: 100%; height: auto; opacity: 0.5;">
        <header><h2 id="welcome">Welcome to Burgers from the Future</h2></header>
  </div>

  <nav><h1 style="text-align: center;">Menu Items</h1> </nav>

  <main>
    <section id="burgers">
      <div class="burgers" style="display: inline-block">
        <h1>Dystopia Burger</h1>
        <img src="https://st.depositphotos.com/1000504/3999/i/950/depositphotos_39996387-stock-photo-burger-bread.jpg" alt="span" style="width: 200px; height: 250px;" title="Dystopia">
        <ul>
            <li>Top bread (<span class="allergen">gluten</span>)</li>
            <li>Bottom bread (<span class="allergen">gluten</span>)</li>
        </ul>
      </div>

      <div class="burgers" style="display: inline-block">
        <h1>Levitating Burger</h1>
        <img src="https://live.staticflickr.com/7855/33697157168_fb75848bd5_b.jpg" alt="span" style="width: 200px; height: 250px;" title="Floating">
        <ul>
            <li>Top bread (<span class="allergen">gluten</span>)</li>
            <li>Lettuce</li>
            <li>Red Onion</li>
            <li>Tomato</li>
            <li>Cheese (<span class="allergen">lactose</span>)</li>
            <li>Ketchup</li>
            <li>Future Sauce</li>
            <li>Beef</li>
            <li>Bottom bread (<span class="allergen">gluten</span>)</li>
        </ul>
      </div>

      <div class="burgers" style="display: inline-block">
        <h1>Everyone-can-eat Burger</h1>
        <img src="https://glutenfreeindian.com/wp-content/uploads/2019/04/PUL_5600.jpg" alt="span" style="width: 200px; height: 250px;" title="Everyone-can-eat">
        <ul>
          <li>Gluten free top bread</li>
          <li>Non-allergenic and vegan contents</li>
          <li>Gluten free bottom bread</li>
        </ul>
      </div>
    </section>

    <section class="userinfo" id="contactpayment">
      <form>
        <p>
          <label for="fullname">Full name</label><br>
          <input type="text" id="firstname" name="fn" required="required" placeholder="First- and Last name">
        </p>

        <p>
          <label for="lastname" type="text">E-mail</label><br>
          <input type="text" id="email" name="ln" placeholder="E-mail address">
        </p>

        <p>
          <label for="lastname">Street</label><br>
          <input type="text" id="streetname" name="street" required="required" placeholder="Street name">
        </p>

        <p>
          <label for="lastname">House Number</label><br>
          <input type="number" id="housenumber" name="housenr" required="required" placeholder="House number">
        </p>
      </form>

      <p>
        <label for="payment">Payment Method</label><br>
        <select id="payment" name="payment">
            <option>Swish</option>
            <option>Betalkort</option>
            <option>Klarna</option>
            <option>PayPal</option>
        </select>
      </p>
    </section>


    <section class="userinfo" id="gender">
      <p>
        <label>Gender</label><br>
        <input type="radio" id="male" name="gender" required="required" placeholder="gender">
        <label for="gender">Male</label><br>
        <input type="radio" id="female" name="gender" required="required" placeholder="gender">
        <label for="gender">Female</label><br>
        <input type="radio" id="other" name="gender" required="required" placeholder="gender">
        <label for="gender">Do not wish to provide</label><br>
      </p>

      <button type="submit" id="sendinfo">
          Send Info
      </button>
    </section>

  </main>



    <footer style="right: 0px; position: absolute; padding: 5px;">
        &copy FutureBurgers, Inc.
    </footer>
  </div>

  <div>
    <div>
      {{ namn }} <br>
      Current delivery time is: {{ deliveryTime }}
      {{ selectedBurger }}
      <Burger v-for="burger in burgers"
              v-bind:burger="burger" 
              v-bind:timeStamp="Date.now().toString()"
              v-on:selected="setSelectedBurger($event)"
              v-bind:key="burger.name"/> <!-- burger (till vänster) skickar lokal variabel till Oneburger 
                                              $-tecken representerar alltid den informationen som kommer TILL meddelandet  
                                              bURGER ÄR En KOMPOnEnT!! dvs som ett block likt en div  --> 
                                              
      <li v-for="burger in burgers" v-bind:key="burger.name">
      {{ burger.name }} 
      <span v-if="burger.kCal > 400">
        BIG MEAL
      </span>
      </li>
    </div>
    <button v-on:click="checkDeliveryTime">Check Delivery Time</button>


    namn: <input type="text" v-model="namn">
    <div id="map" v-on:click="addOrder">
      click here
    </div>
  </div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'

const socket = io();

const MenuItem = function(nm, pic, cals, lcts, gltn) {
  this.name = nm;
  this.URL = pic;
  this.kCal = cals;
  this.lactose = lcts;
  this.gluten = gltn;
}

let infoBurgers = [ new MenuItem("Dystopia Burger", "https://st.depositphotos.com/1000504/3999/i/950/depositphotos_39996387-stock-photo-burger-bread.jpg",
            50, "", "gluten"), new MenuItem("Levitating Burger", "https://live.staticflickr.com/7855/33697157168_fb75848bd5_b.jpg",
            1000, "lactose", "gluten"), new MenuItem("Everyone-can-eat Burger", "https://glutenfreeindian.com/wp-content/uploads/2019/04/PUL_5600.jpg",
            401, "", "")];

console.log(infoBurgers);

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      deliveryTime: 0,
      selectedBurger: [],
      namn: "hejhej", // DESSA HÄnVISAS TILL VIA "this."
      colorList: [ {label: 'Red', color: '#FF0000'}, 
                  {label: 'Green', color: '#00FF00'}],
      burgers: infoBurgers
    }
  },
  created: function () {
      socket.on('deliveryTimeIs', time =>
        this.deliveryTime = time.orders);
    },

  methods: {
    setSelectedBurger: function (burgare) {
      this.selectedBurger.push(burgare.name)
    },
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      console.log(event)
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.offsetX - 10 - offset.x,
                                           y: event.offsetY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    },
    checkDeliveryTime: function() {
      socket.emit("deliveryTime");
    }
  } // socket.emit för att skicka. emit är etablerat språk för att skicka meddelanden. $-tecken för barn till förälder
}
</script>

<style>

  #map {
    width: 500px;
    height: 500px;
    background-color: red;
    cursor: pointer;
  }

  #map:hover {
    background-color: pink;
  }

/*   body {
    font-family: Georgia, 'Times New Roman', Times, serif;
    font-size: 14pt;
    background-color: red;
  } */

  .allburgers {
      left: 5%;
      size: relative;
      background-color: aliceblue;
      display: flex;
      justify-content: first baseline;
  }

  .burgers {
      height: relative;
      width: 270px;
      top: 0;
      padding: 10px;
      border: 2px solid rgb(50, 50, 50);
  }

  #header {
      margin: 5px 0px 10px; overflow: hidden;
  }

  #welcome {
      position: absolute; padding: 5%; margin-top: -64%; margin-left: 28%;
  }

  #burgers {
      background-color: black;
      color: white;
      left: 5%;
      size: relative;
      display: flex;
      justify-content: first baseline;
  }

  .userinfo {
      padding: 5%;
  }

  button {
      margin: 20px 0px 20px;
  }

  p {
      font-size: large;
  }

  h1, h2 {
      position: relative;

  }

  .allergen {
      font-weight: bold;
  }

  button:hover {
      background-color: cornflowerblue;
  }
</style>
