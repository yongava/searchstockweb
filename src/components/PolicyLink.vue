<template>
  <div class="footer">
    <a href="https://www.privacypolicygenerator.info/live.php?token=3bszOxSF61YM4X1WldxfftYfF874T1rN" target="_blank" class="policy-link">Policy</a>
    <p class="copyright">© 2023 YourStockSymbol</p>
  </div>
</template>

<script>
export default {
  name: 'PolicyLink'
};
</script>

<style scoped>
.footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 15px;
  display: flex;
  justify-content: space-between;
  padding: 5px;
  background-color: #f8f9fa;
  align-items: center;
}

.policy-link {
  color: black;
  text-decoration: none;
}

.copyright {
  color: black;
  margin-right: 20px;
}
</style>