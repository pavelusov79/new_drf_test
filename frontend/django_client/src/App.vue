<template>
  <div id="app">
    <EquipmentDrf />
  </div>
</template>

<script>
import EquipmentDrf from './components/EquipmentDrf.vue'

export default {
  name: 'App',
  components: {
    EquipmentDrf
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
