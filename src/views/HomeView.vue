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
        <Burger v-for="burger in burgers" 
                v-bind:burger="burger" 
                v-bind:key="burger.name" 
                v-on:orderedBurger = "addtoOrder($event)"/>
      </div>
    </div>

    <section class="userinfo" id="contactpayment">
      <form>
        <p>
          <label for="fullname"> Full name </label><br>
          <input type="text" id="firstname" v-model="fullname" required="required" placeholder="First- and Last name">
        </p>

        <p>
          <label for="lastname" type="text"> E-mail </label><br>
          <input type="text" id="email" v-model="email" placeholder="E-mail address">
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
        <input type="radio" id="male" v-model="gender" required="required" placeholder="gender">
        <label for="gender"> Male </label><br>
        <input type="radio" id="female" v-model="gender" required="required" placeholder="gender">
        {{ gender }}
        <label for="gender"> Female </label><br>
        <input type="radio" id="other" v-model="gender" required="required" placeholder="gender">
        <label for="gender"> Do not wish to provide </label><br>
      </p>

      <div id="mapwrap"> 
        Pick a delivery point: 
        <div id="map" v-on:click="setLocation">
          <div v-bind:style="{left: location.x + 'px', top: location.y +'px'}" v-bind:key="'dots'">
          </div>
        </div>
      </div>

      <button type="submit" id="sendinfo" v-on:click="orderPressed">
          Send Info
      </button>
    </section>

  

    <button v-on:click="checkDeliveryTime">Check Delivery Time</button>

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
  data: function () {
    return {
      deliveryTime: 0,
      selectedBurger: [],
      namn: "hejhej", // DESSA HÄnVISAS TILL VIA "this."
      colorList: [ {label: 'Red', color: '#FF0000'}, 
                  {label: 'Green', color: '#00FF00'}],
      burgers: menu,
      orderedBurgers: {},
      location: {x: 0,
                 y: 0},
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
                                name: order.name,
                                email: order.email,
                                payment: order.payment,
                                gender: order.gender,
                                details: { x: event.offsetX - 10 - offset.x,
                                           y: event.offsetY - 10 - offset.y },
                                orderItems: order.amountOrdered
                              }
                 );
    },
    orderPressed: function () {
      let order = {name: this.nm, email: this.email, payment: this.payment, gender: this.gender, amountOrdered: this. orderedBurgers};
      console.log(order);
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                name: order.name,
                                email: order.email,
                                payment: order.payment,
                                gender: order.gender,
                                details: { x: event.offsetX - 10 - offset.x,
                                           y: event.offsetY - 10 - offset.y },
                                orderItems: order.amountOrdered
                              })
    },


    checkDeliveryTime: function() {
      socket.emit("deliveryTime");
    },
    setLocation: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().right};
      this.location.x = event.offsetX - 10;
      this.location.y = event.offsetY - 10;
    },
    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },
    printOrder: function () {
      console.log(this.fullname, this.email, this.payment, this.gender);
      console.log(this.orderedBurgers);
      socket.emit("addOrder", {orderId: this.getOrderNumber(),
            details: {
              x: this.location.x,
              y: this.location.y
            },
            orderItems: this.orderedBurgers,
            customerInformation: [this.name, this.email, this.paymet, this.gender]
      });
    }
  } // socket.emit för att skicka. emit är etablerat språk för att skicka meddelanden. $-tecken för barn till förälder
}
</script>

<style>

#map {
    position: relative;
    background-repeat: no-repeat;
    width: 1920px;
    height: 1080px;
    background: url("../../public/img/polacks.jpg");
    cursor: pointer;
  }

#mapwrap {
  width: 86vw;
  height: 60vh;
  margin: 2%;
  overflow: scroll;
}

  /* #map:hover {
    background-color: pink;
  } */

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

  .burger {
      height: relative;
      width: 270px;
      top: 0;
      padding: 10px;
      /* border: 2px solid rgb(50, 50, 50); */
  }

  .burgerpic {
    width: 200px; height: 250px;
  }

  #header {
      margin: 5px 0px 10px; overflow: hidden;
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


  #allergenInfo {
    color: red;
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

  .bold {
      font-weight: bold;
  }

  button:hover {
      background-color: cornflowerblue;
  }
</style>