<template>
  <div id="app" class="container">
    <img alt="Kneron logo" class="logo" src="./assets/logo.png" />
    <cros-test />
  </div>
</template>

<script>
import crosTest from './components/cros-test.vue'

export default {
  name: 'App',
  components: {
    crosTest
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
  margin-top: 0px;
}

.logo {
    max-width: 80%;
    max-height: 80%;
}
</style>
