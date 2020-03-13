<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <TerainApp testProp="Protokół odbioru" />
  </div>
</template>

<script>
import TerainApp from './components/TerainApp.vue'

export default {
  name: 'App',
  components: {
    TerainApp
  }
}
</script>

<style lang="scss">
body{
  background-color: darkslategray !important;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  font-size: 20px;
  margin-top: 60px;
  background-color: darkslategray;
  margin: 0;
  height: 100vh;
  img{
    max-height: 50px;
    object-fit: cover;
    margin-bottom: 50px;
  }
}

</style>
