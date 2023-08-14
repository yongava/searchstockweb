<template>
  <div id="app">

    <SearchCard />
  </div>
</template>

<script>
import SearchCard from './components/SearchCard.vue'

export default {
  name: 'App',
  components: {
    SearchCard
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