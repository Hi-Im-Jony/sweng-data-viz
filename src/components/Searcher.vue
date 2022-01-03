<template>
  <div id="searcher">
    <div id="question-container">
      <h2>What technologies does the</h2>
      <div class="input">
        <v-select
          v-model="selection"
          :items="['user', 'organisation']"
          label="Select"
          dense
          solo
        />
      </div>
      <h2>named</h2>
      <div class="input">
        <v-text-field v-model="name" dense label="Name" solo></v-text-field>
      </div>
      <h2>use most?</h2>
    </div>

    <a v-on:click="callAPI">Search</a>

    <div v-if="error">
      <p class="error">{{ errorMsg }}</p>
    </div>

    <p>{{ info }}</p>
  </div>
</template>
<script>
import axios from "axios";

export default {
  data: () => ({
    info: "",
    name: "",
    selection: "",
    error: false,
    errorMsg: "",
    items: ["Foo", "Bar", "Fizz", "Buzz"],
  }),
  methods: {
    callAPI: function () {
      let nameProvided = !(this.name === "");
      if (this.selection === "") {
        this.errorMsg =
          "Please specify if you want to search for a user or an organisation";
        this.error = true;
      } else {
        this.error = false;
        if (nameProvided) {
          if (this.selection === "organisation") this.getOrgInfo(this.name);
          else this.getUserInfo(this.name);
        } else {
          this.errorMsg = "No name provided";
          this.error = true;
        }
      }
    },
    getUserInfo: function (user) {
      axios
        .get("https://api.github.com/users/" + user)
        .then((response) => (this.info = response.data))
        .catch((error) => {
          if (error.response) {
            this.errorMsg = "Error - no user data found";
            this.error = true;
            console.log(this.info);
          } else this.error = false;
        });
    },
    getOrgInfo: function (org) {
      axios
        .get("https://api.github.com/orgs/" + org)
        .then((response) => (this.info = response.data))
        .catch((error) => {
          if (error.response) {
            this.errorMsg = "Error - no org data found";
            this.error = true;
            console.log(this.info);
          } else this.error = false;
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

<style scoped>
#searcher {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
  border: solid black;
  width: 90%;
  align-self: center;
}
#question-container {
  display: flex;
  justify-content: center;
  width: 100%;
}
.input {
  width: 160px;
  margin: 0 7px 0 7px;
}

a {
  border: solid rgb(41, 100, 168);
  padding: 2px 10px 2px 10px;
  border-radius: 10px;
}
.error {
  margin: 10px;
}
</style>
