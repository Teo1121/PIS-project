<template>
  <Doughnut
    v-if="loaded"
    :chartOptions="chartOptions"
    :chartData="chartData"
    :chartId="chartId"
    :dataset-id-key="datasetIdKey"
    :plugins="plugins"
    :cssClasses="cssClasses"
    :styles="styles"
    :width="width"
    :height="height"
  />
</template>

<script>
import { Doughnut } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  ArcElement,
  CategoryScale,
  LinearScale,
} from "chart.js";
import { reactive } from "@vue/reactivity";

ChartJS.register(
  Title,
  Tooltip,
  Legend,
  ArcElement,
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
  name: "DoughnutChart",
  components: { Doughnut },
  props: {
    chartId: {
      type: String,
      default: "doughnut-chart",
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
            data: this.vueChartData.map((item) => {
              return parseInt(item.numOfReviews);
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
      pieData: reactive([]),
      pieLabel: reactive([]),
      pieColor: reactive([]),
      chartOptions: {
        responsive: true,
      },
    };
  },
  async mounted() {
    /*
    this.loaded = false;

    this.pieData = vueChartData.map((item) => {
      return parseInt(item.averageScore);
    });
    this.pieLabel = vueChartData.map((item) => {
      return item.professorsName;
    });
    this.pieColor = vueChartData.map((item) => {
      return generateRandomColor(item.professorsName);
    });
*/
    this.loaded = true;
  },
};
</script>
