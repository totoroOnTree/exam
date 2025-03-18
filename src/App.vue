<template>
  <ExcelReader />
</template>

<script>
import ExcelReader from './components/ExcelReader.vue'

export default {
  name: 'App',
  components: {
    ExcelReader
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
