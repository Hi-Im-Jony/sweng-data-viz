<template>
  <v-app id="app">
    <hero />

    <searcher />

    <!-- <h2>
      What languages are preffered by
      <input
        class="input"
        id="username-input"
        type="text"
        v-model="username"
      />, at
      <input class="input" id="orgname-input" type="text" v-model="orgname" />?
    </h2>
    <a href="#" v-on:click="callAPI">Search</a>
    <p>{{ info }}</p> -->
    <my-footer />
  </v-app>
</template>

<script>
import Hero from "@/components/Hero.vue";
import MyFooter from "@/components/MyFooter.vue";
import Searcher from "@/components/Searcher.vue";

export default {
  name: "App",
  components: {
    Hero,
    MyFooter,
    Searcher,
  },
  data: () => ({
    info: "",
    username: "Username",
    orgname: "Organisation",
    items: ["Foo", "Bar", "Fizz", "Buzz"],
  }),
};
</script>

<style scoped>
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
  position: relative;
  bottom: 2px;
}
</style>
