<template>
  <div id="app">
    <img alt="TOYA SP. z o.o. LOGO" src="./assets/logo.png">
    <TerainApp title="Protokół odbioru" />
    <div class="footer">Grupa TOYA © 2020</div>
  </div>
</template>

<script>
import TerainApp from './components/TerainApp.vue'

export default {
  name: 'App',
  components: {
    TerainApp
  },
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
  min-height: 88vh;
  position: relative;
  img{
    max-height: 300px;
    object-fit: cover;
    margin-bottom: 50px;
    margin-top: 25px;
  }
  h2{
    font-size: 70px;
  }
  .footer {
    position: absolute;
    font-size: 20px;
    font-family: 'Roboto', sans-serif;
    color: #AAAAAA;
    width: 200px;
    bottom: 10px;
    left: 50%;
    margin-left: -100px;
  }
}

</style>
