<template>
  <div id="app">
  
  <SearchCard /> 
  <AdBanner /> 
  </div>
</template>

<script>
import SearchCard from './components/SearchCard.vue'
import AdBanner from './components/AdBanner.vue'

export default {
  name: 'App',
  components: {
    SearchCard,
    AdBanner
  },
  mounted() {
    const script = document.createElement('script')
    script.src = 'https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-3199261308247865'
    script.async = true
    script.setAttribute('crossorigin', 'anonymous')
    document.head.appendChild(script)
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