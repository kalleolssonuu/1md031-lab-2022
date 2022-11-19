<template>
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
  
  