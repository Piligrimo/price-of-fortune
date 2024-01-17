<template>
  <div id="app">
    <sheet/>
  </div>
</template>

<script>
import Sheet from './components/Sheet.vue'

export default {
  name: 'App',
  components: {
    Sheet
  }
}
</script>

<style>


td {
  font-size: 20px;
}

button, input, select {
  padding: 5px;
  font-size: 24px;
  display: block;
  margin: 5px;
  width: 100%;
}

body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
