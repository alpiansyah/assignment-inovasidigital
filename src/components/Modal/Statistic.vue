<template>
  <div>
    <div
      class="modal fade"
      id="statisticModal"
      tabindex="-1"
      aria-labelledby="statisticModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-xl">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="statisticModalLabel">Statistic</h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="container text-left">
              <div class="row align-items-start">
                <div class="col-md-4" style="display: flex">
                  <select
                    class="form-select"
                    @change="initChart()"
                    v-model="chartType"
                    aria-label="Default select example"
                  >
                    <option value="Country">Country</option>
                    <option value="RSPO Status">RSPO Status</option>
                    <option value="RSPO Type">
                      RSPO Certification Distribution
                    </option>
                  </select>
                </div>
                <div class="col-md-12">
                  <Bar id="chart" :options="chartOptions" :data="chartData" />
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
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

ChartJS.register(Title, Tooltip, BarElement, CategoryScale, LinearScale);

export default {
  name: "statistic-component",
  props: ["themecolor", "datasets"],
  components: { Bar },
  data() {
    return {
      chartType: "Country",
      chartData: {
        labels: [],
        datasets: [
          {
            data: [],
          },
        ],
      },
      chartOptions: {
        responsive: true,
      },
    };
  },
  methods: {
    resetChart() {
      this.chartData = {
        labels: [],
        datasets: [
          {
            data: [],
          },
        ],
      };
    },
    initChart() {
      // sort data per key yang dipilih oleh user dikelompokan dan dihitung jumlah nya
      const getUniques = (array, compareProp) => {
        let byId = {};
        let uniques = [];

        let i = 0;
        let item = {};

        for (i; i < array.length; i++) {
          item = array[i];
          if (byId[item[compareProp]]) {
            byId[item[compareProp]].count++;
          } else {
            byId[item[compareProp]] = item;
            uniques.push(item);
            item.count = 1;
          }
        }

        return uniques;
      };
      let sortedData = getUniques(this.datasets, this.chartType);
      let labels = [];
      let datasets = [];

      // declare data yang sudah disorting kedalam config chart
      sortedData.forEach((element) => {
        labels.push(element[this.chartType]);
        datasets.push(element["count"]);
      });
      this.chartData = {
        labels: labels,
        datasets: [
          {
            fill: true,
            borderColor: "#0096FF",
            borderWidth: 2,
            borderDash: [],
            borderDashOffset: 0.0,
            pointBackgroundColor: "#0096FF",
            pointBorderColor: "rgba(255,255,255,0)",
            pointHoverBackgroundColor: "#0096FF",
            pointBorderWidth: 20,
            pointHoverRadius: 4,
            pointHoverBorderWidth: 15,
            pointRadius: 4,
            data: datasets,
          },
        ],
      };
    },
  },
  mounted() {
    this.initChart();
  },
};
</script>

<style>
#statisticModal {
  filter: v-bind("themecolor");
}
#chartType {
  margin-right: 5px;
}
</style>
