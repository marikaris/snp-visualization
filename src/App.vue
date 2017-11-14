<template>
  <div class="container">
    <div class="row" id="header">
      <div class="col">
        <small id="header-text"><i>Powered by</i></small>
        <img id="header-img" src="/img/logo_molgenis_small.png" alt="molgenis"/>
      </div>
    </div>
    <div id="app">
      <router-view></router-view>
    </div>
  </div>
</template>
<style>
  #header-text {
    font-size: 0.65rem;
    position: absolute;
    top: -0.60rem;
    right: 2rem;
  }
  #header-img {
    position: absolute;
    right: 2rem;
    bottom: -0.40rem;
  }
  #header {
    box-sizing: border-box;
    background-color:#f8f8f8;
    height: 3.5rem;
    padding: 1rem;
    width: 101%;
    top: 0;
    right: 0;
    overflow: hidden;
    position: absolute;
    border-top: outset;
    border-bottom: ridge;
  }
  .card {
    margin-bottom: 1rem;
  }
  #app {
    position: absolute;
    width: 60%;
    margin-top: 5rem;
  }
</style>
<script>
  export default {
    name: 'molgenis-app'
  }
</script>
