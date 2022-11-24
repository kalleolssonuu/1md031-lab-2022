<template>
    <div class="burger" v-bind:key="burger.time">
      <h1> {{ burger.name }} </h1>

      <img class="burgerpic" v-bind:src="burger.imgSrc">
      <ul v-for="(ingredient, key) in burger.ingredients" v-bind:key="key">
          <li> {{ ingredient }} </li>
      </ul>  

      <ul style="list-style-type: square;">
        <span v-for="(allergen, key) in burger.allergens" v-bind:key="key">
          <span v-if="allergen == 'lactose' || allergen == 'gluten'">
            <li id="allergenInfo"> <span> <p class="bold"> contains {{ allergen }} </p> </span>  </li>
          </span>
        </span>
      </ul>
      <div>
        <p>Pris: {{ burger.price }} kr </p>
        Amount: <button v-on:click=decreaseAmount type="button"> - </button>
                <button v-on:click=increaseAmount type="button"> + </button> <br>
        Added: {{ amountOrdered }}
      </div>
    </div>

</template>
  
  <script>
  export default {
    data: function() {
      return {
        amountOrdered: 0
      }
    },  
    name: 'OneBurger',
    props: {
      burger: Object
    },
    methods: {
      increaseAmount: function () {
        this.amountOrdered += 1;
        this.$emit('orderedBurger', {name: this.burger.name, amount: this.amountOrdered
        })
      }, 
      decreaseAmount: function () {
        if (this.amountOrdered > 0) {
          this.amountOrdered -= 1;
        }
        this.$emit('orderedBurger', {name: this.burger.name, amount: this.amountOrdered
        })
      }
    }
  }
  </script>
  
  <!-- Add "scoped" attribute to limit CSS to this component only -->
  <style scoped>
  
  </style>
  
  