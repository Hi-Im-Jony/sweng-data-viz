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
    <a href="#" v-on:click="callAPI">Search</a>
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
      data: [],
    };
  },
  methods: {
    callAPI: function () {
      let usernameProvided = !(this.username === "n/a" || this.username === "");
      let orgnameProvided = !(
        this.orgname === "n/a" ||
        this.orgname === "" ||
        this.orgname === "Organisation"
      );

      if (usernameProvided && !orgnameProvided) {
        this.getUserInfo(this.username);
      } else if (!usernameProvided && orgnameProvided) {
        this.getOrgInfo(this.orgname);
      }
    },
    getUserInfo: function (user) {
      axios
        .get("https://api.github.com/users/" + user)
        .then((response) => (this.info = response.data))
        .catch((error) => {
          if (error.response) {
            this.info = "Error - no user data found";
            console.log(this.info);
          }
        });
    },
    getOrgInfo: function (org) {
      axios
        .get("https://api.github.com/orgs/" + org)
        .then((response) => (this.info = response.data))
        .catch((error) => {
          if (error.response) {
            this.info = "Error - no org data found";
            console.log(this.info);
          }
        });
    },
    getOrgMembers: function (org) {
      axios
        .get("https://api.github.com/orgs/" + org + "/members")
        .then((response) => (this.data = response.data));
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
  position: relative;
  bottom: 2px;
}
</style>
