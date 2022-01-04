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
          :data="this.info.pChartData"
          suffix="%"
          label="Title"
        />
      </div>
      <div id="bchart-container">
        <h3 v-if="this.info.bChartData">
          Number Of Projects A Language Was The "Main" Language
        </h3>
        <column-chart
          xtitle="Main Language"
          ytitle="Number of Repos"
          v-if="this.info.bChartData != null"
          :data="this.info.bChartData"
        />
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";

export default {
  data: () => ({
    info: {
      pChartData: null,
      bChartData: null,
    },
    name: "",
    selection: "",
    error: false,
    errorMsg: "",
    header: {
      headers: {
        Authorization: "token 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",
      },
    },
  }),
  methods: {
    callAPI: function () {
      this.info.pChartData = null;
      this.info.bChartData = null;

      let nameProvided = !(this.name === "");
      if (this.selection === "") {
        this.info.pChartData = null;
        this.info.bChartData = null;
        this.errorMsg =
          "Please specify if you want to search for a user or an organisation";
        this.error = true;
      } else {
        this.error = false;
        if (nameProvided) {
          if (this.selection === "organisation") this.getOrgInfo(this.name);
          else this.getUserInfo(this.name);
        } else {
          this.info.pChartData = null;
          this.info.bChartData = null;
          this.errorMsg = "No  " + this.selection + " name provided";
          this.error = true;
        }
      }
    },
    getUserInfo: function (user) {
      axios
        .get("https://api.github.com/users/" + user, this.header)
        .then((response) => this.getRepos(response))
        .catch((error) => {
          if (error.response) {
            this.info.pChartData = null;
            this.info.bChartData = null;
            this.errorMsg = "Error - no user data found";
            this.error = true;
            this.info = "";
          } else this.error = false;
        });
    },

    getOrgInfo: function (org) {
      axios
        .get("https://api.github.com/orgs/" + org, this.header)
        .then((response) => this.getRepos(response))
        .catch((error) => {
          if (error.response) {
            this.info.pChartData = null;
            this.info.bChartData = null;
            this.errorMsg = "Error - no org data found";
            this.error = true;
            console.log(this.info);
          } else this.error = false;
        });
    },
    getRepos: function (response) {
      let numOfRepos = response.data.public_repos;
      console.log("Num of repos: " + numOfRepos);

      let reposUrl = response.data.repos_url;
      this.parseRepoData(reposUrl, numOfRepos);
    },
    parseRepoData: async function (reposUrl, numOfRepos) {
      let langUrls = [];
      let reposUsingLangL = {};
      let repoPages = [];
      let pageCount = Math.ceil(numOfRepos / 100); // 100 repos per page response
      for (let i = 1; i <= pageCount; ++i) {
        await axios
          .get(reposUrl + "?page=" + i + "&per_page=100", this.header)

          .then((response) => {
            repoPages.push(response.data);
          });
      }
      for (const page in repoPages) {
        for (const repo in repoPages[page]) {
          // get main language of repo
          let mainLang = repoPages[page][repo].language;

          // create object member identifier
          let identifier = '"' + mainLang + '"';

          // check if language is a member of object
          if (identifier in reposUsingLangL) {
            // if so, just increment number of times this language was main language in a repo
            reposUsingLangL[identifier].timesUsed++;
          } else {
            // else, init member
            reposUsingLangL[identifier] = {};
            reposUsingLangL[identifier]["name"] = {}; // have to "init" it to something, so...
            reposUsingLangL[identifier]["name"] = mainLang;
            reposUsingLangL[identifier]["timesUsed"] = 1;
          }

          // add current repos languages to list to analyse later
          let langUrl = repoPages[page][repo].languages_url;
          langUrls.push(langUrl);
        }
      }

      let langMetrics = {};
      for (const langUrl in langUrls) {
        await axios
          .get(langUrls[langUrl] + "", this.header)
          .then((response) => {
            let langs = Object.keys(response.data);
            for (const lang in langs) {
              if (langs[lang] in langMetrics) {
                langMetrics[langs[lang]] =
                  langMetrics[langs[lang]] + response.data[langs[lang]];
              } else {
                langMetrics[langs[lang]] = response.data[langs[lang]];
              }
            }
          });
      }

      let totalMetric = 0;
      for (const metric in langMetrics)
        totalMetric = totalMetric + langMetrics[metric];

      for (const metric in langMetrics)
        langMetrics[metric] = Math.round(
          (langMetrics[metric] / totalMetric) * 100
        ).toFixed(2); // generate percentages

      this.formatData(reposUsingLangL, langMetrics);
    },
    formatData: function (bData, pData) {
      // format barchart data
      let formattedBarData = [];
      for (const data in bData) {
        let formattedData = [bData[data].name, bData[data].timesUsed];
        formattedBarData.push(formattedData);
      }

      // format piechart data
      let formattedPieData = [];
      console.log(pData);
      for (const [key, value] of Object.entries(pData)) {
        let formattedData = [key, value];
        formattedPieData.push(formattedData);
      }

      //console.log(pData);
      this.info.bChartData = formattedBarData;
      this.info.pChartData = formattedPieData;
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
