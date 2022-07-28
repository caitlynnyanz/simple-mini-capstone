<script>
// var axios = require("axios")
import axios from "axios";

export default {
  data: function () {
    return {
      message: "All Products!",
      errors: [],
      currentProduct: {},
      editProduct: {},
      products: [],
      newProduct: {},
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    indexProducts: function () {
      axios.get("http://localhost:3000/products.json").then((response) => {
        this.products = response.data;
        console.log("All.products: ", response.data);
      });
    },
    createProduct: function () {
      axios
        .post("http://localhost:3000/products.json", this.newProduct)
        .then((response) => {
          console.log("Success!", response.data);
          this.products.push(response.data);
          this.newProduct = "";
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showProduct: function (product) {
      console.log(product);
      this.currentProduct = product;
      this.editProduct = product;
      document.querySelector("#product-info").showModal();
    },
    updateProduct: function (productToEdit) {
      axios.patch(`http://localhost:3000/products/${productToEdit.id}.json`, productToEdit).then((response) => {
        console.log(response.data);
      });
    },
    destroyProduct: function (product) {
      axios.delete(`http://localhost:3000/products/${product.id}`).then((response) => {
        console.log(response.data);
        var index = this.products.indexOf(product);
        this.products.splice(index, 1);
      });
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>New Product</h2>
    Name:
    <input type="text" v-model="newProduct.name" />
    Price:
    <input type="text" v-model="newProduct.price" />
    Description:
    <input type="text" v-model="newProduct.description" />
    ImageUrl:
    <input type="text" v-model="newProduct.image_url" />
    <button v-on:click="createProduct()">Create Product</button>
    <div class="errors" v-for="error in errors" v-bind:key="error">
      {{ errors }}
    </div>
    <div v-for="product in products" v-bind:key="product.id">
      <h2>Name: {{ product.name }}</h2>
      <img v-bind:src="product.image_url" v-bind:alt="product.title" />
      <p>Price: {{ product.price }}</p>
      <button v-on:click="showProduct(product)">More Info</button>
    </div>
    <dialog id="product-info">
      <form method="dialog">
        <h2>Product Info:</h2>
        <p>
          Name:
          <input type="text" v-model="currentProduct.name" />
        </p>
        <p>
          Price:
          <input type="text" v-model="currentProduct.price" />
        </p>
        <p>
          Description:
          <input type="text" v-model="currentProduct.description" />
        </p>
        <p>
          Image Url:
          <input type="text" v-model="currentProduct.image_url" />
        </p>
        <button v-on:click="updateProduct(editProduct)">Update</button>
        <button v-on:click="destroyProduct(currentProduct)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style>
.errors {
  color: darkred;
}
.home {
  background-color: rgb(121, 175, 175);
  border: 2px solid rgb(89, 56, 56);
  font-family: "Comic Sans MS", "Comic Sans", cursive;
}
</style>
