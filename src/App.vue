<template>
  <div id="app">
    <ConstructorView msg="Welcome to Constructor"/>
  </div>
</template>

<script>
import ConstructorView from './components/ConstructorView.vue'

export default {
  name: 'App',
  components: {
    ConstructorView
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
