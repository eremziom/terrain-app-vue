<template>
  <div id="app">
    <img alt="TOYA SP. z o.o. LOGO" src="./assets/logo.png">
    <TerainApp title="Protokół odbioru" />
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
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: darkslategray;
  font-size: 50px;
  margin-top: 60px;
  margin: 0;
  img{
    max-height: 50px;
    object-fit: cover;
    margin-bottom: 50px;
    margin-top: 25px;
  }
  h2{
    font-size: 70px;
  }
}

</style>
