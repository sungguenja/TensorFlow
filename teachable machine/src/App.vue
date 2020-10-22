<template>
  <div id="app">
    <AI></AI>
  </div>
</template>

<script>
import AI from './components/AI.vue'
export default {
  name: 'App',
  components: {
    AI
  },
  data() {
    return {
      Id: null
    }
  },
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
