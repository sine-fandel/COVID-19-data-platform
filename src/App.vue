<template>
  <div id="app">
    <Home class="data-box"></Home>
  </div>
</template>

<script>
import Home from "./views/Home"

export default {
  components: {
    Home
  }
}
</script>

<style lang="scss">
#app {
  height: 16500px;
  width: 100%;
  position: absolute;
  top: 0px;
  bottom: 0px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background-attachment: fixed;
  background-color: #10aeb5;
}
</style>
