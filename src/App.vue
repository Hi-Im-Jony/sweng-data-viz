<template>
  <div id="app">
    <hero />
    <h2>What languages are preffered by *username*, at *orgname*?</h2>

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
    };
  },
  methods: {
    callAPI: function (username) {
      axios
        .get("https://api.github.com/" + username)
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
</style>
