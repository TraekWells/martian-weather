<template>
  <div id="app">
    <input type="text" v-model="search" />
    <p>{{ search }}</p>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      search: null,
    };
  },
  mounted() {
    axios
      .get(
        `https://api.nasa.gov/insight_weather/?api_key=${process.env.VUE_APP_API_KEY}&feedtype=json&ver=1.0`
      )
      .then((response) => console.log(response));
  },
};
</script>

<style lang="scss">
#app {
  height: 100vh;
}
</style>
