<template>
  <div id="app">
    <!-- <HelloWorld/> -->
    <MultiSelect/>
  </div>
</template>

<script>
import MultiSelect from './components/MultiSelect/index.vue'

export default {
  name: 'App',
  components: {
    MultiSelect
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
  margin-top: 60px;
}
</style>
