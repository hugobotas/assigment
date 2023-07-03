<script setup>


async function fetchData() {

  const response = await fetch('https://api.ipma.pt/open-data/observation/meteorology/stations/observations.json')
  const data = await response.json();
  let lisbon = {};
  for (const hour in data) {
     lisbon = {
      ...lisbon,
      [hour]: data[hour]['1200579']
    }
  }
  console.log(lisbon);
}
</script>


<template>
  <div class="greetings">
    <h1 class="green">HEYY</h1>
    <button type="button" @click="fetchData">Click Me!</button>
  </div>
</template>



<!--<style scoped>-->
<!--h1 {-->
<!--  font-weight: 500;-->
<!--  font-size: 2.6rem;-->
<!--  position: relative;-->
<!--  top: -10px;-->
<!--}-->

<!--h3 {-->
<!--  font-size: 1.2rem;-->
<!--}-->

<!--.greetings h1,-->
<!--.greetings h3 {-->
<!--  text-align: center;-->
<!--}-->

<!--@media (min-width: 1024px) {-->
<!--  .greetings h1,-->
<!--  .greetings h3 {-->
<!--    text-align: left;-->
<!--  }-->
<!--}-->
<!--</style>-->
