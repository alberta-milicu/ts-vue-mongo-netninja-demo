<template>
  <v-app class="full-app">
    <v-content>
      <v-container class="recipe-container">
        <v-row>
          <v-col
            v-for="recipe in recipes"
            :key="recipe.id"
            cols="12"
            md="6"
            lg="4"
          >
            <v-card
              class="recipe-box"
              @click="showRecipe(recipe)"
              elevation="20"
            >
              <v-card-title class="recipe-name">{{ recipe.name }}</v-card-title>
            </v-card>
          </v-col>
          <v-btn class="add-btn-plus" color="primary" @click="addRecipeDialog">
            +
          </v-btn>
        </v-row>
        <v-dialog v-model="dialogVisible" max-width="500">
          <v-card v-if="recipe">
            <v-card-title>{{ recipe.name }}</v-card-title>
            <v-card-text>Ingredients: {{ recipe.ingredients }}</v-card-text>
            <v-card-actions>
              <div class="text-right">
                <v-btn @click="recipe && deleteRecipe(recipe.id)">Delete</v-btn>
                <v-btn color="#000000b8" dark @click="closeDialog()"
                  >Close</v-btn
                >
              </div>
            </v-card-actions>
          </v-card>
          <v-card v-else>
            <v-card-title>Add Recipe</v-card-title>
            <v-card-text>
              <v-text-field
                label="Name"
                v-model="newName"
                required
              ></v-text-field>
              <v-text-field
                label="Ingredients"
                v-model="newIngredients"
                required
              ></v-text-field>
            </v-card-text>
            <v-card-actions>
              <div class="btn-box">
                <v-btn @click="closeDialog">Cancel</v-btn>
                <v-btn class="add-btn" color="primary" @click="addRecipe"
                  >Add</v-btn
                >
              </div>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-container>
    </v-content>
  </v-app>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import axios from "axios";

interface Recipe {
  id: string;
  name: string;
  ingredients: string;
}

@Component
export default class MyComponent extends Vue {
  recipes: Recipe[] = [];
  newName = "";
  newIngredients = "";
  dialogVisible = false;
  recipe: Recipe | null = null;

  mounted() {
    this.getRecipes();
  }

  async getRecipes() {
    const result = await axios.get("http://localhost:3000/recipes/");
    this.recipes = result.data.map((res: any) => ({
      id: res._id,
      name: res.name,
      ingredients: res.ingredients,
    }));
  }

  async addRecipe() {
    const newRecipe: Recipe = {
      name: this.newName,
      ingredients: this.newIngredients,
      id: "",
    };
    await axios.post("http://localhost:3000/recipes/", newRecipe);
    this.newName = "";
    this.newIngredients = "";
    this.getRecipes();
    this.dialogVisible = false;
  }
  async deleteRecipe(id: string) {
    console.log("Entered delete");
    await axios.delete("http://localhost:3000/recipes/", {
      data: { id: id },
    });
    this.recipe = null;
    this.dialogVisible = false;
    this.getRecipes();
  }
  async addRecipeDialog() {
    this.dialogVisible = true;
    this.recipe = null;
    this.newName = "";
    this.newIngredients = "";
  }

  showRecipe(recipe: Recipe) {
    this.recipe = recipe;
    this.dialogVisible = true;
  }

  closeDialog() {
    this.recipe = null;
    this.dialogVisible = false;
  }
}
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Delicious+Handrawn&display=swap");

.mx-auto {
  margin-left: auto;
  margin-right: auto;
}

.recipe-container {
  font-family: "Delicious Handrawn", cursive;
  margin-top: 200px;
}

.add-btn-plus {
  margin-left: 160px;
  margin-top: 50px;
  border-radius: 100px;
  font-size: 25px;
  font-weight: bold;
  font-family: "Courier New";
}

.recipe-name {
  display: flex;
  justify-content: center;
  font-size: 40px;
}

.text-right {
  justify-content: right;
}

.recipe-box {
  height: 100px;
}

.btn-box {
  justify-content: flex-end;
}
</style>
