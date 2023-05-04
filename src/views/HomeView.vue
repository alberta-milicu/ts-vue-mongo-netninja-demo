<template>
  <ul>
    <li>
      <div class="container-div">
        <div>
          <h1>Unlimited virtual food source...</h1>
          <br /><br />
          <h2>just one click away!</h2>
          <br /><br /><br />
          <div class="button-container">
            <v-btn class="recipes-btn" color="primary" v-on:click="toRecipes"
              >To Recipes</v-btn
            >
          </div>
        </div>
        <div class="center-image">
          <img src="../assets/images/food.jpg" height="600px" />
        </div>
      </div>
    </li>
  </ul>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
@Component
export default class MyComponent extends Vue {
  toRecipes() {
    if (localStorage.getItem("token")) this.$router.push("/recipes");
  }
}
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Delicious+Handrawn&display=swap");

ul {
  list-style-type: none;
  display: inline-block;
}

h1 {
  margin-top: 0px;
  margin-left: 10px;
  text-align: center;
  font-family: "Delicious Handrawn", cursive;
  font-size: 45px;
}

h2 {
  margin-left: 400px;
  font-family: "Delicious Handrawn", cursive;
  font-size: 30px;
  color: #000000b8;
}

.button-container {
  margin-left: 240px;
  font-family: "Delicious Handrawn", cursive;
}

.recipes-btn {
  width: 160px;
  height: 70px;
  font-size: 24px;
  font-weight: bold;
}

.center-image {
  width: 350px;
  height: 0px;
}
.container-div {
  width: 100%;
  padding: 10px 0px;
  margin-top: 0px;
  display: flex;
  align-items: center;
}
.container-div .center-image {
  margin: 0 auto;
  margin-top: -350px;
  margin-left: 80px;
}

.container-div > div:first-child {
  padding-top: 300px;
}
</style>
