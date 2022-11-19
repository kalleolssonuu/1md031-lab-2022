<template>
    <div>
      <div>
        Current delivery time is: {{ deliveryTime }}
        {{ selectedBurger }}
        <Burger v-for="burger in burgers"
                v-bind:burger="burger" 
                v-bind:timeStamp="Date.now().toString()"
                v-on:selected="setSelectedBurger($event)"
                v-bind:key="burger.name"/> <!-- burger (till vänster) skickar lokal variabel till Oneburger 
                                                $-tecken representerar alltid den informationen som kommer TILL meddelandet  --> 
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
        burgers: [ {name: "small burger", kCal: 250},
                   {name: "standard burger", kCal: 450},
                   {name: "large burger", kCal: 850}
                 ]
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
  </style>