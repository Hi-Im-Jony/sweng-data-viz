<template>
  <div id="app">
    <hero />
    <h2>
      What languages are preffered by
      <input
        class="input"
        id="username-input"
        type="text"
        v-model="username"
      />, at
      <input class="input" id="orgname-input" type="text" v-model="orgname" />?
    </h2>
    <v-btn v-on:click="callAPI">Search</v-btn>
    <p>{{ info }}</p>
    <my-footer />
  </div>
</template>

<script>
import Hero from "@/components/Hero.vue";
import MyFooter from "@/components/MyFooter.vue";
import axios from "axios";
export default {
  name: "App",
  components: {
    Hero,
    MyFooter,
  },
  data: function () {
    return {
      info: "",
      username: "Username",
      orgname: "Organisation",
    };
  },
  methods: {
    callAPI: function () {
      axios
        .get("https://api.github.com/users/" + this.username)
        .then((response) => (this.info = response));
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  min-height: 100vh;
  position: relative;
}
.input {
  border: solid black;
  border-radius: 10px;
  width: 160px;
  overflow: scroll;
  color: rgb(48, 85, 119);
  text-align: center;
}
</style>
