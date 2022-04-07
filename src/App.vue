<template>
  <div id="app">
    <!--<img alt="Vue logo" src="https://raw.githubusercontent.com/chainapsis/keplr-example/master/keplr-logo.png">-->
    <keplrStart/>
  </div>
</template>

<script>
import keplrStart from './components/keplrStart.vue'

export default {
  name: 'App',
  components: {
    keplrStart
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
