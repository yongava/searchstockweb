<template>
  <div id="app">
    <SearchCard class="content" />
    <AdBanner class="content" />
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
  text-align: center;
  color: #2c3e50;
  display: flex;
  flex-direction: column;
  justify-content: space-around;  /* UPDATED */
  min-height: 100vh;
}

.content {
  flex: 1 0 auto;  /* ADDED */
}

@media (max-width: 768px) {
  #app {
    max-width: 100%;
  }
}
</style>