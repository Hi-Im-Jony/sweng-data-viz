<template>
  <v-app id="app">
    <div id="content">
      <hero />
      <searcher />
    </div>
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
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
#content {
  min-height: 100vh !important;
  position: relative !important;
  padding: 0 0 60px 0 !important;
  display: flex !important;
  flex-direction: column !important;
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
