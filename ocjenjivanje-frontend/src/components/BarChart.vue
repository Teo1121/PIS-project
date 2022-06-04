<template>
  <Bar
    v-if="loaded"
    :chart-options="chartOptions"
    :chart-data="chartData"
    :chart-id="chartId"
    :dataset-id-key="datasetIdKey"
    :plugins="plugins"
    :css-classes="cssClasses"
    :styles="styles"
    :width="width"
    :height="height"
  />
</template>

<script>
import axios from "axios";
import { Bar } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";
import { reactive } from "@vue/reactivity";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
);

function generateRandomColor(str) {
  var hash = 0;
  for (var i = 0; i < str.length; i++) {
    hash = str.charCodeAt(i) + ((hash << 5) - hash);
  }
  var colour = "#";
  for (var i = 0; i < 3; i++) {
    var value = (hash >> (i * 8)) & 0xff;
    colour += ("00" + value.toString(16)).substr(-2);
  }
  return colour;
}

export default {
  name: "BarChart",
  components: { Bar },
  props: {
    chartId: {
      type: String,
      default: "bar-chart",
    },
    datasetIdKey: {
      type: String,
      default: "label",
    },
    width: {
      type: Number,
      default: 400,
    },
    height: {
      type: Number,
      default: 400,
    },
    cssClasses: {
      default: "",
      type: String,
    },
    styles: {
      type: Object,
      default: () => {},
    },
    plugins: {
      type: Object,
      default: () => {},
    },
    vueChartData: {
      type: Array,
      default: [],
    },
  },
  computed: {
    chartData() {
      return {
        datasets: [
          {
            indexAxis: "x",
            label: "",
            data: this.vueChartData.map((item) => {
              return parseFloat(item.averageScore);
            }),
            backgroundColor: this.vueChartData.map((item) => {
              return generateRandomColor(item.professorsName);
            }),
          },
        ],
        labels: this.vueChartData.map((item) => {
          return item.professorsName;
        }),
      };
    },
  },
  data() {
    return {
      loaded: false,
      barData: reactive([]),
      barLabel: reactive([]),
      barColor: reactive([]),
      chartOptions: {
        responsive: true,
      },
    };
  },
  mounted() {
    this.loaded = true;
  },
};
</script>
