<template>
  <div class="home">
    <h3>{{ message }}</h3>
    <div>
      <h4>Please add another place:</h4>
      <input
        type="text"
        v-model="newPlaceParams.name"
        placeholder="name"
      /><br />
      <input
        type="text"
        v-model="newPlaceParams.address"
        placeholder="address"
      /><br />
      <button v-on:click="placeCreate()">Add Place</button>
    </div>
    <div>
      <p v-for="place in places" v-bind:key="place.id">
        <strong>{{ place.name }}</strong>
        <br />
        {{ place.address }} <br />
        <button v-on:click="placeShow(place)">Edit</button>
      </p>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h4>Edit this place:</h4>
        <input type="text" v-model="currentPlace.name" /><br />
        <input type="text" v-model="currentPlace.address" /><br />
        <button v-on:click="placeUpdate()">Update</button>
        <!-- <button v-on:click="placeDestroy()">Delete</button> -->
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      message:
        "Welcome to the app that shows places, specifically places that are restaurants",
      places: [],
      newPlaceParams: {},
      currentPlace: "",
    };
  },
  created: function () {
    this.placeIndex();
  },
  methods: {
    placeIndex: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log("Places", response.data);
        this.places = response.data;
      });
    },
    placeCreate: function () {
      axios
        .post("http://localhost:3000/places", this.newPlaceParams)
        .then((response) => {
          console.log(response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
      this.newPlaceParams = {};
    },
    placeShow: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    placeUpdate: function () {
      console.log(this.currentPlace);
      axios
        .patch(
          `http://localhost:3000/places/${this.currentPlace.id}`,
          this.currentPlace
        )
        .then((response) => {
          console.log("Place successfully updated!", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>
