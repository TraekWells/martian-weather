<template>
  <div id="app">
    <input type="text" v-model="search" />
    <p>{{ search }}</p>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      search: null
    };
  }
};
</script>

<style lang="scss">
#app {
  height: 100vh;
}
</style>
