<template>
  <div class="container">
    <notes />
  </div>
</template>

<script>
import Notes from "./components/Notes.vue";
export default {
  name: "App",
  components: {
    Notes,
  },
};
</script>

<style lang="scss">
* {
  transition: all 1s;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
