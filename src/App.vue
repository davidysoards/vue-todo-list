<template>
  <div id="app">
    <section class="section">
      <div class="container has-text-centered">
        <img alt="Vue logo" src="./assets/logo.png" width="100px" />
        <h1 class="title is-1">ToVue List</h1>
      </div>
    </section>
    <ToVue />
  </div>
</template>

<script>
import ToVue from "./components/ToVue.vue";

export default {
  name: "app",
  components: {
    ToVue
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #222222;
}
</style>
