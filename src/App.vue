<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your TEST APP"/>
    <TestCompo testProp="TOYA Sp. Z o.o." />
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import TestCompo from './components/TestCompo.vue'

export default {
  name: 'App',
  components: {
    HelloWorld,
    TestCompo
  }
}
</script>

<style lang="scss">
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
    height: 150px;
  }
}
</style>
