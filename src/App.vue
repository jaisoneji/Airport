<template>
  <div id="app">
    <Airport msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Airport from './components/Airport.vue'

export default {
  name: 'App',
  components: {
    Airport
  }
}
</script>

<style>
#app {
  
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width:100vw;
  height:100%;
  background-color: #f4f4f4;
  margin:0;
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
