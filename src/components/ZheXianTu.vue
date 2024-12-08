<template>
  <div class="ZheXianTu">
    <div id="main"></div>
  </div>
</template>

<script>
import * as echarts from "echarts";

export default {
  name: "ZheXianTu",
  props: {
    versionData: Array, // 接收传递过来的版本数据
    chartTitle: String,  // 接收传递过来的标题
  },
  watch: {
    versionData(newData) {
      if (newData && newData.length > 0) {
        this.updateChart(newData);
      }
    },
    chartTitle(newTitle) {
      if (newTitle) {
        this.updateTitle(newTitle);
      }
    },
  },
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      const chartDom = document.getElementById("main");
      this.myChart = echarts.init(chartDom);

      // 初始图表配置
      const option = {
        title: {
          left: "center",
          text: this.chartTitle || "姓氏数量变化", // 初始标题
        },
        tooltip: {
          trigger: "axis",
        },
        xAxis: {
          type: "category",
          boundaryGap: false,
        },
        yAxis: {},
        series: [
          {
            name: "数量变化",
            type: "line",
            showSymbol: false,
            data: [],
            lineStyle: {
              width: 2,
            },
            areaStyle: {
              color: {
                type: "linear",
                x: 0,
                y: 0,
                x2: 0,
                y2: 1,
                colorStops: [
                  { offset: 0, color: "rgba(58,77,233,0.8)" },
                  { offset: 1, color: "rgba(58,77,233,0.2)" },
                ],
              },
            },
          },
        ],
      };

      this.myChart.setOption(option);
    },

    updateChart(versionData) {
      const labels = versionData.map(item => item.version);
      const data = versionData.map(item => item.count);

      const option = {
        xAxis: {
          data: labels,  // 设置 x 轴为版本年代
        },
        series: [
          {
            name: "数量变化",
            type: "line",
            showSymbol: false,
            data: data,  // 设置数据
            lineStyle: {
              width: 2,
            },
            areaStyle: {
              color: {
                type: "linear",
                x: 0,
                y: 0,
                x2: 0,
                y2: 1,
                colorStops: [
                  { offset: 0, color: "rgba(58,77,233,0.8)" },
                  { offset: 1, color: "rgba(58,77,233,0.2)" },
                ],
              },
            },
          },
        ],
      };

      this.myChart.setOption(option);
    },

    updateTitle(newTitle) {
      this.myChart.setOption({
        title: {
          text: newTitle, // 更新标题
        },
      });
    },
  },
};
</script>

<style scoped>
.ZheXianTu {
  width: 100%;
  text-align: center;
}

#main {
  width: 60%;
  height: 600px;
  margin: 0 auto;
  background: #fff;
}
</style>
