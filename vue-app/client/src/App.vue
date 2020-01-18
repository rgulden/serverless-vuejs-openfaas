<template>
  <div id="app">
    <b-card class="text-center">
      <img alt="Vue logo" src="./assets/logo.png" />
      <HelloWorld msg="Welcome to GuldenTech's Serverless Vue.js App" />
    </b-card>
    <br>
    <br>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "app",
  components: {
    HelloWorld
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
