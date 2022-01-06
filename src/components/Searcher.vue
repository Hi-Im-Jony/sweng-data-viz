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
    <div id="charts-container">
      <div v-if="this.info.pChartData" id="pchart-container">
        <h3>Percentage Of Code In "X" Language</h3>
        <pie-chart
          id="pchart"
          :data="this.info.pChartData.percentages"
          suffix="%"
          :legend="false"
        />
      </div>
      <div id="bchart-container">
        <h3 v-if="this.info.bChartData != {}">
          Number Of Projects A Language Was The "Main" Language
        </h3>
        <column-chart
          xtitle="Main Language"
          ytitle="Number of Repos"
          v-if="this.info.bChartData != {}"
          :data="this.info.bChartData"
        />
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import Vue from "vue";

export default {
  data: () => ({
    info: {
      pChartData: {
        metrics: {},
        percentages: {},
      },
      bChartData: {},
    },
    name: "",
    selection: "",
    error: false,
    errorMsg: "",
    header: {
      headers: {
        Authorization: "token <input_token>",
      },
    },
  }),
  methods: {
    callAPI: function () {
      this.info.pChartData = { metrics: { total: 0 }, percentages: {} };
      this.info.bChartData = {};

      let nameProvided = !(this.name === "");
      if (this.selection === "") {
        this.info.pChartData = { metrics: { total: 0 }, percentages: {} };
        this.info.bChartData = {};
        this.errorMsg =
          "Please specify if you want to search for a user or an organisation";
        this.error = true;
      } else {
        this.error = false;
        if (nameProvided) {
          if (this.selection === "organisation") this.getOrgInfo(this.name);
          else this.getUserInfo(this.name);
        } else {
          this.info.pChartData = { metrics: { total: 0 }, percentages: {} };
          this.info.bChartData = {};
          this.errorMsg = "No  " + this.selection + " name provided";
          this.error = true;
        }
      }
    },
    getUserInfo: function (user) {
      this.info.pChartData = { metrics: { total: 0 }, percentages: {} };
      this.info.bChartData = {};
      axios
        .get("https://api.github.com/users/" + user, this.header)
        .then((response) => this.getRepoPages(response))
        .catch((error) => {
          if (error.response) {
            this.errorMsg = "Error - no user data found";
            this.error = true;
            this.info = "";
          } else this.error = false;
        });
    },

    getOrgInfo: function (org) {
      this.info.pChartData = { metrics: { total: 0 }, percentages: {} };
      this.info.bChartData = {};
      axios
        .get("https://api.github.com/orgs/" + org, this.header)
        .then((response) => this.getRepoPages(response))
        .catch((error) => {
          if (error.response) {
            this.errorMsg = "Error - no org data found";
            this.error = true;
          } else this.error = false;
        });
    },
    getRepoPages: function (response) {
      // Num of total public repos we will analyse
      const numOfRepos = response.data.public_repos;
      console.log("Num of repos: " + numOfRepos);
      // Num of "pages" (containing repo info) we will receive from API
      const pageCount = Math.ceil(numOfRepos / 100); // 100 repos per page response
      // Url to repo infor
      const reposUrl = response.data.repos_url;

      // For each page we need
      for (let i = 1; i <= pageCount; ++i) {
        axios
          // Request the page
          .get(reposUrl + "?page=" + i + "&per_page=100", this.header)
          // Parse page info
          .then((pageReceived) => {
            this.parseData(pageReceived.data);
          });
      }
    },
    parseData: async function (page) {
      for (const repo in page) {
        const currentRepo = page[repo];
        const langsUrl = currentRepo.languages_url;
        // Get lang data for this repo
        await axios
          .get(langsUrl, this.header)
          // Then parse the data
          .then((repoLangs) => {
            const langs = Object.keys(repoLangs.data);
            // continue if there are viable langs in the repo
            if (langs.length != 0) {
              /* Parse Bar Chart Info */
              // main language is always first
              let mainLang = langs[0];
              // construct main lang identifier
              let langID = '"' + mainLang + '"';
              // change "null" to other
              if (mainLang === null) langID = '"Other"';
              // check if language already "on record"
              if (langID in this.info.bChartData) {
                // just update value
                this.info.bChartData[langID]++;
              } else {
                // add this language to records
                Vue.set(this.info.bChartData, langID, 1);
              }

              /* Parse Pie Chart Info */
              // get new metrics
              for (const lang in repoLangs.data) {
                // construct identifier for lang
                langID = '"' + lang + '"';
                // obtain metric value from api
                const metric = repoLangs.data[lang];
                // add to total
                this.info.pChartData.metrics.total += metric;
                // if lang already "on record"
                if (langID in this.info.pChartData.metrics) {
                  // just update value
                  this.info.pChartData.metrics[langID] += metric;
                } else {
                  // add lang to records
                  Vue.set(this.info.pChartData.metrics, langID, metric);
                  Vue.set(this.info.pChartData.percentages, langID, 0);
                }
              }
              // re-do percentages with new metrics
              for (const lang in this.info.pChartData.percentages) {
                // calculate percentages
                let percentage = this.info.pChartData.metrics[lang];
                percentage /= this.info.pChartData.metrics.total;
                percentage = (percentage * 100).toFixed(2);

                Vue.set(this.info.pChartData.percentages, lang, percentage);
              }
            }
          });
      }
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
  width: 90%;
  align-self: center;
  padding: 10px;
}
#question-container {
  display: flex;
  justify-content: center;
  width: 100%;
}
#charts-container {
  display: flex;
  align-content: center;
  justify-content: space-evenly;
  width: 100%;
  margin: 20px;
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
#pchart-container {
  display: flex;
  flex-direction: column;
}
#bchart-container {
  display: flex;
  flex-direction: column;
}
</style>
