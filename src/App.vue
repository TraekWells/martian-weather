<template>
  <div id="app">
    <h1>Martian Weather</h1>
    <p>
      Currently, it's {{ currentTemp }}° on Mars with a high of {{ hiTemp }}°
      and a low of {{ lowTemp }}°.
    </p>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      search: null,
      currentTemp: null,
      hiTemp: null,
      lowTemp: null,
    };
  },
  methods: {
    // Convert the temperatures to Fahrenheit
    convertToFahrenheit(degrees) {
      return degrees * (9 / 5) + 32;
    },
  },
  mounted() {
    axios
      .get(
        `https://api.nasa.gov/insight_weather/?api_key=${process.env.VUE_APP_API_KEY}&feedtype=json&ver=1.0`
      )
      .then((response) => {
        // Get the sols available in the data
        const sols = response.data.sol_keys;
        // Map the current sol to the current average temperature. I'm making an assumption here that the last item in the array is the current day and the previous six items in the array are the previous six days
        this.currentTemp = this.convertToFahrenheit(
          response.data[sols[6]].AT.av
        );
        this.hiTemp = this.convertToFahrenheit(response.data[sols[6]].AT.mx);
        this.lowTemp = this.convertToFahrenheit(response.data[sols[6]].AT.mn);
      });
  },
};
</script>

<style lang="scss">
#app {
  height: 100vh;
}
</style>
