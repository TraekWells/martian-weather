<template>
  <div id="app">
    <h1>Martian Weather</h1>
    <p>
      Currently, it's {{ currentTemp }}° on Mars with a high of {{ hiTemp }}°
      and a low of {{ lowTemp }}°.
    </p>
    <canvas id="chart" height="500" width="500"></canvas>
  </div>
</template>

<script>
import axios from "axios";
import Chart from "chart.js";

export default {
  name: "App",
  data() {
    return {
      search: null,
      currentTemp: null,
      hiTemp: null,
      lowTemp: null,
      sols: []
    };
  },
  methods: {
    // Convert the temperatures to Fahrenheit
    convertToFahrenheit(degrees) {
      return degrees * (9 / 5) + 32;
    },
    generateChart() {
      // const labels = [];
      // this.sols.forEach(sol => {
      //   console.log(sol);
      // });
      const ctx = document.getElementById("chart");
      const lineChart = new Chart(ctx, {
        type: "line",
        data: {
          labels: ["Red", "Blue", "Yellow", "Green", "Purple", "Orange"],
          datasets: [
            {
              data: [this.hiTemp, 4],
              borderWidth: 1
            }
          ]
        }
      });

      this.chart = lineChart;
    }
  },
  mounted() {
    axios
      .get(
        `https://api.nasa.gov/insight_weather/?api_key=${process.env.VUE_APP_API_KEY}&feedtype=json&ver=1.0`
      )
      .then(response => {
        // Get the sols available in the data
        const sols = response.data.sol_keys;

        // Convert data to array
        const dataArray = Object.entries(response.data);
        // Loop through data object to get the previous six days
        dataArray.forEach((data, index) => {
          if (index <= 5) {
            // Create a new object to structure the sol data
            const dataObject = {};
            // Add the sol day as the first item in new object
            dataObject[0] = data[0];
            // Add the atmospheric temperature object as the second item in the new object
            dataObject[1] = data[1].AT;
            // Push the new object to the sol array
            this.sols.push(dataObject);
          }
        });

        // Map the current sol to the current average temperature
        this.currentTemp = this.convertToFahrenheit(
          response.data[sols[6]].AT.av
        );
        this.hiTemp = this.convertToFahrenheit(response.data[sols[6]].AT.mx);
        this.lowTemp = this.convertToFahrenheit(response.data[sols[6]].AT.mn);
      });
    this.generateChart();
  }
};
</script>

<style lang="scss">
#app {
  height: 100vh;
}
</style>
