<template>
  <div id="ad-container">
    <!-- The Infolinks ad will be placed here -->
  </div>
</template>

<script>
export default {
  mounted(){
    this.loadAd();
  },
  methods: {
    loadAd(){
      let script1 = document.createElement('script')
      script1.type = 'text/javascript'
      script1.textContent = `var infolinks_pid = 3402867; var infolinks_wsid = 0;`

      let script2 = document.createElement('script')
      script2.type = 'text/javascript'
      script2.src = "//resources.infolinks.com/js/infolinks_main.js"

      let containerElement = document.querySelector('#ad-container')
      containerElement.appendChild(script1)
      containerElement.appendChild(script2)
    }
  }
}
</script>

<style scoped>
#add-container {
  width: 100%; // Set this to the desired width
  height: auto; // Set this to the desired height
}
</style>