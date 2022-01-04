<template>
  <div id="hero">
    <h1 id="sub-title">CSU33012 - Software Engineering Metric Visualisation</h1>
    <p>
      Using the GitHub REST API, we can ask the question that many computer find
      out what the most used and preffered technologies are, by individuals or
      organisations. This is important, as it can help you choose which
      technologies to pick up next!
      <br />
      We use publicly available information, so private repos are ommitted from
      results.
    </p>
  </div>
</template>

<style scoped>
* {
  padding: 0;
  margin: 0;
}
#hero {
  margin: 0 0 20px 0;
  width: 100vw;
  background-image: linear-gradient(
    to right,
    rgb(56, 127, 175),
    rgb(53, 139, 139),
    rgb(54, 173, 173)
  );
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-evenly;
  text-align: center;
  min-height: 25vh;
  padding: 10px 0 10px 0;
}
p {
  width: 50%;
}
</style>
