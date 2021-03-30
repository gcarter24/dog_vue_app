<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <p>
      Name:
      <input type="text" v-model="newDogName" />
    </p>
    <p>
      Age:
      <input type="text" v-model="newDogAge" />
    </p>
    <p>
      Breed:
      <input type="text" v-model="newDogBreed" />
    </p>
    <p>
      Size:
      <input type="text" v-model="newDogSize" />
    </p>
    <p><button v-on:click="createDog">Create Dog</button></p>
    <div v-for="dog in dogs" v-bind:key="dog.id">
      <p>{{ dog.name }}</p>
      <button v-on:click="showDog(dog)">Dog Facts</button>
      <hr />
    </div>
    <dialog id="dog-details">
      <form method="dialog">
        <p>
          Name:
          <input type="text" v-model="currentDog.name" />
        </p>
        <p>
          Age:
          <input type="text" v-model="currentDog.age" />
        </p>
        <p>
          Breed:
          <input type="text" v-model="currentDog.breed" />
        </p>
        <p>
          Size:
          <input type="text" v-model="currentDog.size" />
        </p>
        <button v-on:click="updateDog(currentDog)">Update</button>
        <button>Close</button>
        <button v-on:click="destroyDog(currentDog)">Remove</button>
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
      message: "Welcome to the Dog App!",
      dogs: [],
      newDogName: "",
      newDogAge: "",
      newDogBreed: "",
      newDogSize: "",
      currentDog: {},
    };
  },
  created: function () {
    this.indexDogs();
  },
  methods: {
    indexDogs: function () {
      console.log("index");
      axios.get("http://localhost:3000/api/dogs").then((response) => {
        console.log(response.data);
        this.dogs = response.data;
      });
    },
    createDog: function () {
      console.log("create");
      var params = {
        name: this.newDogName,
        age: this.newDogAge,
        breed: this.newDogBreed,
        size: this.newDogSize,
      };

      axios.post("http://localhost:3000/api/dogs", params).then((response) => {
        console.log(response.data);
        this.dogs.push(response.data);
        this.newDogName = "";
        this.newDogAge = "";
        this.newDogBreed = "";
        this.newDogSize = "";
      });
    },
    showDog: function (theDog) {
      console.log("show");
      console.log(theDog);
      this.currentDog = theDog;
      document.querySelector("#dog-details").showModal();
    },
    updateDog: function (theDog) {
      console.log(theDog);
      var params = {
        name: theDog.name,
        age: theDog.age,
        breed: theDog.breed,
        size: theDog.size,
      };
      axios.patch("/api/dogs/" + theDog.id, params).then((response) => {
        console.log(response.data);
      });
    },
    destroyDog: function (theDog) {
      console.log(theDog);
      axios.delete("/api/dogs/" + theDog.id).then((response) => {
        console.log(response.data);
        var index = this.dogs.indexOf(theDog);
        this.dogs.splice(index, 1);
      });
    },
  },
};
</script>
