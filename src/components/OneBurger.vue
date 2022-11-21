<template>


    <div class="burgers" v-for="burger in burgers" v-bind:key="burger.name">
      <h1> {{ burger.name }} </h1>
      <img class="burgerpic" src=burger.URL>
      <ul v-for="ingredient in burger.ingredients">
        <li v-if="ingredient == (lactose || gluten)"> {{ ingredient }} </li>
      </ul> 
    </div>

    <div class="burgers" style="display: inline-block">
        <h1> {{ burger.name }} </h1>
        <img src=burger.URL alt="span" style="width: 200px; height: 250px;" title="Dystopia">
        <ul>
            <li>Top bread (<span class="allergen">gluten</span>)</li>
            <li>Bottom bread (<span class="allergen">gluten</span>)</li>
        </ul>
    </div>


    <div>
      {{ burger.name }}, {{ burger.kCal }} kCal {{ burger.lactose }} {{ burger.gluten }}
      <button v-on:click="selectThisBurger">Select</button>
    </div>
  </template>
  
  <script>
  export default {
    name: 'OneBurger',
    props: {
      burger: Object
    },
    methods: {
      selectThisBurger: function () {
        this.$emit("selected", this.burger)
      }
    }
  }
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  
  </style>
  
  