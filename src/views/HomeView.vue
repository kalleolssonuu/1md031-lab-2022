<template>
  <div>
  <div id="header">
        <img src="https://wallpaperaccess.com/full/1373403.jpg" alt="span" style="width: 100%; height: auto; opacity: 0.5;">
        <header><h2 id="welcome">Welcome to Burgers from the Future</h2></header>
  </div>

  <nav><h1 style="text-align: center;"> Menu Items </h1> </nav>

  <main>
    <div>
      <div class="wrapper">                     <!-- burger (till vänster) skickar lokal variabel till Oneburger 
                                                $-tecken representerar alltid den informationen som kommer TILL meddelandet  
                                                bURGER ÄR En KOMPOnEnT!! dvs som ett block likt en div  --> 
        <Burger v-for="(burger, key) in burgers" :key='key' 
                v-bind:burger="burger" 
                v-bind:key="key" 
                v-on:orderedBurger = "addToOrder($event)"/>
      </div>
    </div>

    <section class="userinfo" id="contactpayment">
      <form>
        <p>
          <label> Full name </label><br>
          <input type="text" v-model="name" required="required" placeholder="First- and Last name">
        </p>

        <p>
          <label> E-mail </label><br>
          <input type="text" v-model="email" placeholder="E-mail address">
        </p>

<!--         <p>
          <label for="lastname"> Street </label><br>
          <input type="text" id="streetname" v-model="street" required="required" placeholder="Street name">
        </p>

        <p>
          <label for="lastname"> House Number </label><br>
          <input type="number" id="housenumber" v-model="housenr" required="required" placeholder="House number">
        </p> -->
      </form>

      <p>
        <label for="payment"> Payment Method </label><br>
        <select id="payment" v-model="payment">
            <option> Swish </option>
            <option> Betalkort </option>
            <option> Klarna </option>
            <option> PayPal </option>
        </select>
      </p>
    </section>


    <section class="userinfo" id="gender">
      <p>
        <label> Gender </label><br>
        <input type="radio" id="male" v-model="gender" value="Male" required="required" placeholder="gender">
        <label for="male"> Male </label><br>
        <input type="radio" id="female" v-model="gender" value="Female" required="required" placeholder="gender">
        <label for="female"> Female </label><br>
        <input type="radio" id="other" v-model="gender" value="Other" required="required" placeholder="gender">
        <label for="other"> Do not wish to provide </label><br>
      </p>
      
      <div id="mapwrap"> 
        Pick a delivery point: 
        <div id="map" v-on:click="setLocation($event)">
          <div v-bind:style="{left: location.x + 'px', top: location.y +'px', position:'relative'}" v-bind:key="'dots'">
          T
          </div>
        </div>
      </div>

      <button type="submit" id="sendinfo" v-on:click="orderPressed">
          Send Info
      </button>
    </section>

    <footer style="right: 0px; position: absolute; padding: 5px;">
        FutureBurgers, Inc.
    </footer>
    </main>
  </div>

</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io();

const MenuItem = function(nm, pic, cals, allrgns, ingrdnts) {
  this.name = nm;
  this.imgSrc = pic;
  this.kCal = cals;
  this.allergens = allrgns;
  this.ingredients = ingrdnts;
}

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function() {
    return {
      deliveryTime: 0,
      selectedBurger: [],
      burgers: menu,
      orderedBurgers: {},
      location: {x: 0,
                 y: 0},
      name: "",
      email: "",
      payment: "",
      gender: "",
    }
  },
  created: function() {
      socket.on('deliveryTimeIs', time =>
        this.deliveryTime = time.orders);
    },

  methods: {
    setSelectedBurger: function(burgare) {
      this.selectedBurger.push(burgare.name)
    },

    getOrderNumber: function() {
      return Math.floor(Math.random()*100000);
    },

    addOrder: function(event) {
      console.log(event)
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                name: order.name,
                                email: order.email,
                                payment: order.payment,
                                gender: order.gender,
                                details: { x: event.offsetX - 10,
                                           y: event.offsetY - 10},
                                orderItems: order.amountOrdered
                              }
                 );
    },

    orderPressed: function (event) {
      let order = {name: this.name, email: this.email, payment: this.payment, gender: this.gender, amountOrdered: this.orderedBurgers};
      console.log(order, {details: { x: this.location.x,
                                     y: this.location.y}});
      socket.emit("addOrder", {orderId: this.getOrderNumber(),
          details: {
          x: this.location.x,
          y: this.location.y
          },
          orderItems: this.orderedBurgers,
          customerInformation: [this.name, this.email, this.payment, this.gender]
      });
    },

    checkDeliveryTime: function() {
      socket.emit("deliveryTime");
    },

    setLocation: function(event) {
      console.log(location.x, location.y)
      this.location.x = event.offsetX;
      this.location.y = event.offsetY;
    },

    addToOrder: function(event) {
      this.orderedBurgers[event.name] = event.amount; // event.name = nyckel, amount = värde
    },

    printOrder: function() {
      console.log(this.name, this.email, this.payment, this.gender);
      console.log(this.orderedBurgers);
    
    }
  } // socket.emit för att skicka. emit är etablerat språk för att skicka meddelanden. $-tecken för barn till förälder
}
</script>

<style>

  #allergenInfo {
    color: red;
  }

  .bold {
      font-weight: bold;
  }

  .burger {
      height: relative;
      width: 270px;
      top: 0;
      padding: 10px;
  }

  .burgerpic {
    width: 200px; height: 250px;
  }

  button {
      margin: 20px 0px 20px;
  }

  button:hover {
      background-color: cornflowerblue;
  }

  h1, h2 {
      position: relative;

  }

  #header {
      margin: 5px 0px 10px; overflow: hidden;
  }

  #map {
      position: relative;
      background-repeat: no-repeat;
      width: 1920px;
      height: 1078px;
      background: url("../../public/img/polacks.jpg");
      cursor: pointer;
    }

  #mapwrap {
    width: 86vw;
    height: 60vh;
    margin: 2%;
    overflow: scroll;
  }

  p {
      font-size: large;
  }

  .userinfo {
      padding: 5%;
  }

  #welcome {
      position: absolute; padding: 5%; margin-top: -64%; margin-left: 28%;
  }

  .wrapper {
      background-color: black;
      color: white;
      left: 5%;
      size: relative;
      display: grid;
      grid-template-columns: 33% 33% 33%;

      /* grid-auto-columns: auto; */
  }
</style>