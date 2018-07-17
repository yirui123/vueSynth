<template>
<div id="app">
  <main>
    <router-view></router-view>
  </main>
</div>
</template>

<script>
export default {
  name: "app"
};
</script>

<style>
body {
  margin: 0;
  /* cursor: url("assets/cat.png"), auto; */
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
