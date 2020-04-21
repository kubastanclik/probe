<template>
  <div id="app">
    <img class="main__logo" alt="Probe logo" src="./assets/logo.svg">
    <Main></main>
    <div class="author"><a href="https://github.com/kubastanclik/" targer="_blank">Created by KS</a></div>
  </div>
</template>

<script>
import Main from './components/Main.vue'

export default {
  name: 'App',
  components: {
    Main
  }
}
</script>

<style>
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.author {
  display: block;
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 10px 0px 10px 0px;
  text-align: center;
  color: #fff;
  background-color: #800000;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 17px;
}

.author a {
  text-decoration: none;
  color: #fff;
  font-size: 13px;
  font-weight: 600;
}

.main__logo {
  max-width: 300px;
}
</style>
