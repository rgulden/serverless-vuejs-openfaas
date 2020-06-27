<template>
  <div id="app">
    <b-card class="text-center">
      <p><strong>{{ reply }}</strong></p>
    </b-card>
    <br>
    <br>
  </div>
</template>

<script>
import axios from 'axios';
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "app",
  components: {
    HelloWorld
  },
  data() {
    return {
      reply: "",
    }
  },
  created() {
    axios.get(`/daily_Status`)
    .then(response => {
      this.reply = response.data
    })
    .catch(e => {
      console.log(e)
    })
  }
}
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
