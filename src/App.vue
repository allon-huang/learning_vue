<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view/>
  </div>
</template>
<style>
  #app {
    text-align: center;
    color: #2c3e50;
    margin-top: 80px;
  }
</style>
<script>
  export default {
    name: 'App',
    data(){
      return{

      }
    }
  }
</script>


