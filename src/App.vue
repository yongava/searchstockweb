<template>
  <div id="app">
    <SearchCard /> 
    <AdBanner /> 
    <PolicyLink />
  </div>
</template>

<script>
import SearchCard from './components/SearchCard.vue'
import AdBanner from './components/AdBanner.vue'
import PolicyLink from './components/PolicyLink.vue'

export default {
  name: 'App',
  components: {
    SearchCard,
    AdBanner,
    PolicyLink
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
  position: relative;   /* Required for the PolicyLink to stick to the bottom */
  min-height: 100vh;    /* Required for the PolicyLink to stick to the bottom */
}
</style>