<template>
  <div class="ad-container">
      <ins class="adsbygoogle"
          style="display:block"
          :data-ad-client="adClient"
          :data-ad-slot="adSlot"
          data-ad-format="auto"
          data-full-width-responsive="true">
      </ins>
  </div>
</template>

<script>
export default {
  data() {
    return {
      adClient: "ca-pub-3199261308247865",
      adSlot: "1001" // replace with your ad slot ID
    };
  },
  mounted() {
    (window.adsbygoogle = window.adsbygoogle || []).push({});
  },
}
</script>
