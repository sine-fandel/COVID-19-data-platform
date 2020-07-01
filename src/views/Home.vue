<template>
<div class="home">
  
  <section class="covid19-box">
    <Domestic></Domestic>
    <National></National>
    <News></News>
    <!-- <test></test> -->
  </section>
</div>
</template>

<script>
// @ is an alias to /src
import Domestic from "../views/Domestic"
import National from "../views/National"
// import test from "../components/test"
import News from "../components/News"

export default {
  name: 'Home',
  data () {
    return {
      activeName: 'first',
    };
  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event);
    }
  },

  components: {
    Domestic,
    National,
    News,
    // test
  }
}
</script>

<style lang="scss">

.covid19-box {
  border-radius: .625rem;
  background-color: white;
  width: 850px;
  position: absolute;
  left: 330px;
  top: 100px;

}
</style>
