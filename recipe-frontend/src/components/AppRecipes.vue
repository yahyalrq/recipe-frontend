<template>
  <div class="jumbotron vertical-center">
    <div class="container">
      <div class="row">
        <div class="col-sm-12">
          <h1>Recipes</h1>
          <hr />
          <br />
          <!-- Allert Message -->
          <b-alert v-if="showMessage" variant="success" show>{{
              message
          }}</b-alert>
          <!-- b-alert v-if="error" variant="danger" show>{{ error }}</b-alert-->

          <button type="button" class="btn btn-success btn-sm" v-b-modal.recipe-modal>
            Create Recipe
          </button>
          <br /><br />
          <table class="table table-hover">
            <thead>
              <tr>
                <th scope="col">Recipe Name</th>
                <th scope="col">Recipe Ingredients</th>
                <th scope="col">Recipe steps</th>
                <th scope="col">Recipe rate</th>
                <th scope="col">Favorite</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="recipe in recipes" :key="recipe.id">
                <td>{{ recipe.name }}</td>
                <td>{{ recipe.ingredients }}</td>
                <td>{{ recipe.steps }}</td>
                <td>{{ recipe.rate }}</td>
                <td> <span v-if="recipe.favorite == 'true'"><input type="checkbox" id="form-edit-favorite-group"
                      v-model="recipe.favorite"></span>
                  <span v-else>
                    <input type="checkbox" id="form-edit-favorite-group">
                  </span>
                </td>
                <!--<td><input type="checkbox" id="form-edit-favorite-group" value="true"></td>-->

                <td>
                  <div class="btn-group" role="group">
                    <button type="button" class="btn btn-info btn-sm" v-b-modal.edit-recipe-modal
                      @click="editRecipe(recipe)">
                      Edit
                    </button>
                    <button type="button" class="btn btn-danger btn-sm" @click="deleteRecipe(recipe)">
                      Delete
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
          <footer class="text-center">
            Copyright &copy; All Rights Reserved.
          </footer>
        </div>
      </div>
      <b-modal ref="addRecipeModal" id="recipe-modal" title="Create a new recipe" hide-backdrop hide-footer>
        <b-form @submit="onSubmit" class="w-100">
          <b-form-group id="form-name-group" label="Recipe Name:" label-for="form-name-input">
            <b-form-input id="form-name-input" type="text" v-model="createRecipeForm.name" placeholder="Recipe Name"
              required>
            </b-form-input>
          </b-form-group>
          <b-form-group id="form-ingredients-group" label="Ingredients:" label-for="form-ingredients-input">
            <b-form-input id="form-ingredients-input" type="text" v-model="createRecipeForm.ingredients" placeholder=" "
              required>
            </b-form-input>
          </b-form-group>
          <b-form-group id="form-steps-group" label="Steps:" label-for="form-steps-input">
            <b-form-input id="form-steps-input" type="text" v-model="createRecipeForm.steps" placeholder=" " required>
            </b-form-input>
          </b-form-group>
          <b-form-group id="form-rate-group" label="Rate:" label-for="form-rate-input">
            <b-form-input id="form-rate-input" type="text" v-model="createRecipeForm.rate" placeholder=" " required>
            </b-form-input>
          </b-form-group>
          <b-form-group id="form-favorite-group" label="Favorite:" label-for="form-favorite-input">
            <!--<input type="checkbox" v-model="toggle" true-value="yes" false-value="no">-->
            <input type="checkbox" id="form-favorite-group" v-model="createRecipeForm.favorite">
            <span>Selected: {{ createRecipeForm.favorite }}</span>
          </b-form-group>


          <b-button type="submit" variant="outline-info">Submit</b-button>
        </b-form>
      </b-modal>
      <!-- End of Modal for Create Account-->
      <!-- Start of Modal for Edit Account-->
      <b-modal ref="editRecipeModal" id="edit-recipe-modal" title="Edit the account" hide-backdrop hide-footer>
        <b-form @submit="onSubmitUpdate" class="w-100">
          <b-form-group id="form-edit-name-group" label="Recipe Name:" label-for="form-edit-name-input">
            <b-form-input id="form-edit-name-input" type="text" v-model="editRecipeForm.name"
              placeholder="Recipe Name"></b-form-input>
          </b-form-group>
          <b-form-group id="form-edit-ingredients-group" label="Ingredients:" label-for="form-edit-ingredients-input">
            <b-form-input id="form-edit-ingredients-input" type="text" v-model="editRecipeForm.ingredients"
              placeholder=" " required>
            </b-form-input>
          </b-form-group>
          <b-form-group id="form-edit-steps-group" label="Steps:" label-for="form-edit-steps-input">
            <b-form-input id="form-edit-steps-input" type="text" v-model="editRecipeForm.steps" placeholder=" "
              required>
            </b-form-input>
          </b-form-group>
          <b-form-group id="form-edit-rate-group" label="Rate:" label-for="form-edit-rate-input">
            <b-form-input id="form-edit-rate-input" type="text" v-model="editRecipeForm.rate" placeholder=" " required>
            </b-form-input>
          </b-form-group>
          <b-form-group id="form-edit-rate-group" label="Rate:" label-for="form-edit-rate-input">
            <span v-if="editRecipeForm.favorite == 'true'">
              <input type="checkbox" id="form-edit-favorite-group" v-model="editRecipeForm.favorite">
            </span>
            <span v-else>
              <input type="checkbox" id="form-edit-favorite-group">
            </span>
          </b-form-group>
          <!--<b-form-group id="form-edit-favorite-group" label="Favorite:" label-for="form-edit-favorite-input">
            <input type="checkbox" id="form-edit-favorite-group" v-model="editRecipeForm.favorite" value="false">
            <span>Selected: {{ editRecipeForm.favorite }}</span>
          </b-form-group>-->
          <b-button type="submit" variant="outline-info">Update</b-button>
        </b-form>
      </b-modal>
      <!-- End of Modal for Edit Account-->
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "AppRecipes",
  data() {
    return {
      recipes: [],
      createRecipeForm: {
        name: "",
        ingredients: "",
        steps: "",
        rate: 0,
        favorite: "",
      },
      editRecipeForm: {
        id: "",
        name: "",
        ingredients: "",
        steps: "",
        rate: 0,
        favorite: "",
      },
      showMessage: false,
      message: "",
    };
  },
  methods: {
    /***************************************************
     * RESTful requests
     ***************************************************/

    //GET function
    RESTgetRecipes() {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes`;
      axios
        .get(path)
        .then((response) => {
          this.recipes = response.data.recipes;
        })
        .catch((error) => {
          console.error(error);
        });
    },

    // POST function
    RESTcreateRecipe(payload) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes`;
      axios
        .post(path, payload)
        .then((response) => {
          this.RESTgetRecipes();
          // For message alert
          this.message = "Recipe Created succesfully!";
          // To actually show the message
          this.showMessage = true;
          // To hide the message after 3 seconds
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
          this.RESTgetRecipes();
        });
    },

    // Update function
    RESTupdateRecipe(payload, recipeId) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes/${recipeId}`;
      axios
        .put(path, payload)
        .then((response) => {
          this.RESTgetRecipes();
          // For message alert
          this.message = "Recipe Updated succesfully!";
          // To actually show the message
          this.showMessage = true;
          // To hide the message after 3 seconds
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
          this.RESTgetRecipes();
        });
    },

    // Delete account
    RESTdeleteRecipe(recipeId) {
      const path = `${process.env.VUE_APP_ROOT_URL}/recipes/${recipeId}`;
      axios
        .delete(path)
        .then((response) => {
          this.RESTgetRecipes();
          // For message alert
          this.message = "Recipe Deleted succesfully!";
          // To actually show the message
          this.showMessage = true;
          // To hide the message after 3 seconds
          setTimeout(() => {
            this.showMessage = false;
          }, 3000);
        })
        .catch((error) => {
          console.error(error);
          this.RESTgetRecipes();
        });
    },

    /***************************************************
     * FORM MANAGEMENT
     * *************************************************/

    // Initialize forms empty
    initForm() {
      this.createRecipeForm.name = "";
      this.createRecipeForm.ingredients = "";
      this.createRecipeForm.steps = "";
      this.createRecipeForm.rate = 0;
      this.createRecipeForm.favorite = "";
      this.editRecipeForm.id = "";
      this.editRecipeForm.name = "";
      this.editRecipeForm.ingredients = "";
      this.editRecipeForm.steps = "";
      this.editRecipeForm.rate = 0;
      this.editRecipeForm.favorite = "";
    },
    // Handle submit event for create account
    onSubmit(e) {
      e.preventDefault();
      //prevent default form submit form the browser
      this.$refs.addRecipeModal.hide(); //hide the modal when submitted
      const payload = {
        name: this.createRecipeForm.name,
        ingredients: this.createRecipeForm.ingredients,
        steps: this.createRecipeForm.steps,
        rate: this.createRecipeForm.rate,
        favorite: this.createRecipeForm.favorite,

      };
      this.RESTcreateRecipe(payload);
      this.initForm();
    },
    change() {
      if (this.editRecipeForm.favorite == "true") {
        return this.editRecipeForm.favorite = "false";
      } else {
        return this.editRecipeForm.favorite = "true";
      };
    },
    // Handle submit event for edit account
    onSubmitUpdate(e) {
      e.preventDefault(); //prevent default form submit form the browser
      this.$refs.editRecipeModal.hide(); //hide the modal when submitted
      if (this.editRecipeForm.favorite == "false") {
        this.editRecipeForm.favorite = "true";
      }
      const payload = {
        name: this.editRecipeForm.name,
        ingredients: this.editRecipeForm.ingredients,
        steps: this.editRecipeForm.steps,
        rate: this.editRecipeForm.rate,
        favorite: this.editRecipeForm.favorite,
      };
      this.RESTupdateRecipe(payload, this.editRecipeForm.id);
      this.initForm();
    },

    // Handle edit button
    editRecipe(recipe) {
      this.editRecipeForm = recipe;
    },

    // Handle Delete button
    deleteRecipe(recipe) {
      this.RESTdeleteRecipe(recipe.id);
    },
  },

  /***************************************************
   * LIFECYClE HOOKS
   ***************************************************/
  created() {
    this.RESTgetRecipes();
  },
};
</script>
