<template>
    <div class="burger" v-bind:key="burger">
      <h1> {{ burger.name }}
        <span v-for="(allergen, index) in burger.allergens" v-bind:key="index"> 
          <span v-if="allergen == 'lactose' || allergen == 'gluten'"> 
            - contains <p class="allergen"> {{ allergen }} </p>
          </span>
        </span>
      </h1>

      <img class="burgerpic" v-bind:src="burger.imgSrc">
      <ul v-for="ingredient in burger.ingredients" v-bind:key="ingredient">
        <li> {{ ingredient }} </li>
      </ul> 
    </div>
<!-- 
    <div>
      {{ burger.name }}, {{ burger.kCal }} kCal {{ burger.lactose }} {{ burger.gluten }}
      <button v-on:click="selectThisBurger">Select</button>
    </div> -->


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
  
  