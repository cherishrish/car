<template>
  <div class="bar">
    <div  id="bar1"></div>
    <div  id="bar2"></div>
  </div>
</template>

<script>
    export default {
        name: "Bar"
    }
</script>

<style scoped>
  .bar {
    position: relative;

  }

  .bar #bar1 {
    position: absolute;
    width: 100%;
    height: 50%;
    background: #003da8;
  }
  .bar #bar2 {
    position: absolute;
    width: 100%;
    top:50%;
    height: 50%;
    background: #22ee22;
  }

</style>
