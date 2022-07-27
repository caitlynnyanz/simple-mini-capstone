<script>
// var axios = require("axios")
import axios from "axios";

export default {
  data: function () {
    return {
      message: "All Products!",
      errors: [],
      currentProduct: {},
      products: [],
      newProductName: "",
      newProductPrice: 0,
      newProductDescription: "",
      newProductImageUrl: "",
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
      console.log("New product created!");
      var params = {
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        image_url: this.newProductImageUrl,
      };
      axios
        .post("http://localhost:3000/products.json", params)
        .then((response) => {
          console.log("Success!", response.data);
          this.products.push(response.data);
          (this.newProductName = ""),
            (this.newProductPrice = 0),
            (this.newProductDescription = ""),
            (this.newProductImageUrl = "");
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
    showProduct: function (product) {
      console.log(product);
      this.currentProduct = product;
      document.querySelector("#product-info").showModal();
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h2>New Product</h2>
    Name:
    <input type="text" v-model="newProductName" />
    Price:
    <input type="text" v-model="newProductPrice" />
    Description:
    <input type="text" v-model="newProductDescription" />
    ImageUrl:
    <input type="text" v-model="newProductImageUrl" />
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
        <p>Name: {{ currentProduct.name }}</p>
        <p>Price: {{ currentProduct.price }}</p>
        <p>Description: {{ currentProduct.description }}</p>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style>
.errors {
  color: darkred;
}
</style>
