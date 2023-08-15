<template>
  <div class="ads">
    <!-- Insert the ad component markup here. -->
  </div>
</template>

<script>
export default {
  name: 'AdBanner',
  mounted() {
    (window.adsbygoogle = window.adsbygoogle || []).push({});
  },
};
</script>

<style scoped>
/* Insert your ad component styles here. */
</style>