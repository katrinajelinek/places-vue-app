<template>
  <div class="home">
    <h1>Places!</h1>

  <div>
    <h2>New Place</h2>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
    Name: <input type="text" v-model="newPlaceName"> <br>
    Address: <input type="text" v-model="newPlaceAddress"> <br>
    <button v-on:click="createPlace()">Create Place</button> <br> <br>
  </div>
    

    <div v-for="place in places">
      <h3>Name: {{ place.name }}</h3>
      <p>Address: {{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button> <br> <br>
    </div>

    <dialog id="recipe-details">
      <form method="dialog">
        <h2>Place Info</h2>
        <p>Name: <input type="text" v-model="currentPlace.name"></p> <br>
        <p>Address: <input type="text" v-model="currentPlace.address"></p> <br>
        <button v-on:click="updatePlace(currentPlace)">Edit</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>

  </div>
</template>

<style>
</style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: []
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then(response => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };

      axios.post("/api/places", params).then(response => {
        console.log(response.data);
        this.places.push(response.data);
        this.newPlaceName = "";
        this.newPlaceAddress = "";
      });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#recipe-details").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address 
      };

      axios.patch(`/api/places/${place.id}`).then(response => {
        console.log(response.data);
      }).catch(error => {
        console.log(error.response.data.errors);
      });
    },
    destroyPlace: function (place) {
      axios.delete(`/api/places/${place.id}`).then(response => {
        console.log("Success", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    }
  },
};
</script>