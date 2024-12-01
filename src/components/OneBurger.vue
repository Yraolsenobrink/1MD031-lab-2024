<template>
  <div class="burger-item">
    <h4>{{ burger.name }}</h4>
    <img v-bind:src="burger.picture" />
    <ul>
      <li>{{ burger.kCal }} kcal</li>
      <li v-if="burger.gluten">
        Innehåller <span class="allergy">gluten</span>
      </li>
      <li v-if="burger.lactose">
        Innehåller <span class="allergy">laktos</span>
      </li>
      <li v-if="burger.sugar">
        Innehåller <span class="allergy">socker</span>
      </li>
      <li v-if="burger.gelatine">
        Innehåller <span class="allergy">gelatin</span>
      </li>
    </ul>
    <p>Antal beställda: {{ amountOrdered }}</p>

    <section>
      <button type="button" v-on:click="addBurger">
        <img
          src="https://cdn-icons-png.flaticon.com/512/32/32339.png"
          style="width:10px; height:auto;"
        />
      </button>
      <button type="button" v-on:click="decrease">
        <img
          src="https://cdn-icons-png.flaticon.com/512/25/25232.png"
          style="width:10px; height:auto;"
        />
      </button>
    </section>
  </div>
</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: {
      type: Object,
      required: true,
    },
  },
  data: function () {
    return {
      amountOrdered: 0, // Startvärdet för antal beställda
    };
  },
  methods: {
    addBurger: function () {
      this.amountOrdered++; // Ökar värdet med 1
      this.$emit('orderedBurger', { name:   this.burger.name, 
                                amount: this.amountOrdered 
                              }
  );
    },
    decrease: function () {
      if (this.amountOrdered > 0) {
        this.amountOrdered--; // Minskar värdet med 1, men inte under 0
      }
    },
  },
};
</script>

<style scoped>
.allergy {
  font-weight: bold;
}

.burger-item {
  padding: 10px;
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 260px;
}

.burger-item img {
  width: auto;
  max-width: 200px;
  height: 200px;
}

.burger-item ul {
  text-align: left; /* Ändra textjustering till vänster */
}

section button {
  margin: 5px;
  cursor: pointer;
  border: 2px solid rgb(91, 3, 3); /* Specificera både bredd, stil och färg */
  border-radius: 5px; /* Lägg till rundade hörn */
}
</style>
