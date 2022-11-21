<template>
  <div class="">
    <!-- <D3Js /> -->
    <D32Js />
  </div>
</template>

<script>
import ChartJs from './components/--chart-js.vue'
import D3Js from './components/d3-js.vue'
import D32Js from './components/d3-2-js.vue'

// import PlanetChart from './components/PlanetChart.vue'

export default {
  name: 'App',
  components: {
    ChartJs,
    D3Js,
    D32Js,
  },
}
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 60px;
  color: #2c3e50;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
}
</style>
