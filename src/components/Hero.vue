<template>
  <div id="hero">
    <h1 id="title">Data Viz</h1>
    <h3 id="sub-title">CSU33012 - Software Engineering Metric Visualisation</h3>
    <p>
      Hello there, welcome to Data Viz - a Data Visualisation Project. A
      question that many people ponder is: what's trendy at the moment?
      Specifically, as programmers and computer scientists, we often wonder,
      what technologies are popular, and who are they popular with? Data Viz was
      designed to answer this question, by using the GitHub REST API, to
      identify the most popular languages within an organisation, or, to see
      what your favourite contributors enjoy using!
    </p>
  </div>
</template>

<style scoped>
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
  height: 25vh;
}
p {
  width: 50%;
}
</style>
