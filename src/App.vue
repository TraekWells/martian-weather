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
      chart: null
    };
  },
  methods: {
    // Convert the temperatures to Fahrenheit
    convertToFahrenheit(degrees) {
      return degrees * (9 / 5) + 32;
    },
    generateChart() {
      const ctx = document.getElementById("chart");
      const lineChart = new Chart(ctx, {
        type: "line",
        data: {
          labels: ["Red", "Blue", "Yellow", "Green", "Purple", "Orange"],
          datasets: [
            {
              label: "# of Votes",
              data: [12, 19, 3, 5, 2, 3],
              backgroundColor: [
                "rgba(255, 99, 132, 0.2)",
                "rgba(54, 162, 235, 0.2)",
                "rgba(255, 206, 86, 0.2)",
                "rgba(75, 192, 192, 0.2)",
                "rgba(153, 102, 255, 0.2)",
                "rgba(255, 159, 64, 0.2)"
              ],
              borderColor: [
                "rgba(255, 99, 132, 1)",
                "rgba(54, 162, 235, 1)",
                "rgba(255, 206, 86, 1)",
                "rgba(75, 192, 192, 1)",
                "rgba(153, 102, 255, 1)",
                "rgba(255, 159, 64, 1)"
              ],
              borderWidth: 1
            }
          ]
        },
        options: {
          scales: {
            yAxes: [
              {
                ticks: {
                  beginAtZero: true
                }
              }
            ]
          }
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

        // Map the current sol to the current average temperature. I'm making an assumption here that the last item in the array is the current day and the previous six items in the array are the previous six days
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
