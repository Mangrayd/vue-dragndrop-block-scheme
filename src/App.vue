<template>
  <div id="app">
    <Drop/>
  </div>
</template>

<script>

import Drop from './components/Drop'

export default {
  name: 'App',
  components: {
    Drop
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
