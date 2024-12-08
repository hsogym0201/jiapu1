  <template>
    <div class="ZhuZhuangTu">
      <!-- 提示占位内容 -->
    <div id="zhuzhuangtu" style="height: 600px;width: 800px"></div>
    </div>
  </template>

  <script>
  import * as echarts from 'echarts';
  export default {
    name: "ZhuZhuangTu",

    props: {
      versionTypeData: Array, //接受传递过来的版本类型数据
      ChartTitle: String,  // 接收传递过来的标题
    },
    watch: {
      versionTypeData(newData) {
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
        this.getchart();
      },
      methods: {
        //图表方法
        getchart() {
          const chartDom = document.getElementById('zhuzhuangtu');
          this.myChart = echarts.init(chartDom);

          const option = {
            title: {
              text: this.ChartTitle ||"家谱版本类型统计",//标题
              left: 'center'
            },
            tooltip: {
              trigger: 'item',
              formatter: '{a} <br/>{b} : {c} ({d}%)'
            },
            legend: {
              type: 'scroll',
              orient: 'vertical',
              right: 10,
              top: 20,
              bottom: 20,
              data: [],//右列的竖着排列的版本类型数据
            },
            series: [
              {
                name: '版本类型',
                type: 'pie',
                radius: '55%',
                center: ['40%', '50%'],
                data: [],//饼的数据
                emphasis: {
                  itemStyle: {
                    shadowBlur: 10,
                    shadowOffsetX: 0,
                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                  }
                }
              }
            ]
          };

          option && this.myChart.setOption(option);
        },

        updateChart(versionTypeData) {
          const legendData = versionTypeData.map(item => item.version);
          const seriesData = versionTypeData.map(item => ({
            name: item.version,
            value: item.count
          }));

          const option = {
            title: {
              text: this.ChartTitle ||"家谱版本类型统计",//标题
              left: 'center'
            },
            tooltip: {
              trigger: 'item',
              formatter: '{a} <br/>{b} : {c} ({d}%)'
            },
            legend: {
              type: 'scroll',
              orient: 'vertical',
              right: 10,
              top: 20,
              bottom: 20,
              data: legendData//右列的竖着排列的版本类型数据
            },
            series: [
              {
                name: '版本类型',
                type: 'pie',
                radius: '55%',
                center: ['40%', '50%'],
                data: seriesData,//饼的数据
                emphasis: {
                  itemStyle: {
                    shadowBlur: 10,
                    shadowOffsetX: 0,
                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                  }
                }
              }
            ]
          }
          option && this.myChart.setOption(option);
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
  .ZhuZhuangTu {
    width: 100%;
    text-align: center;
  }
  #zhuzhuangtu{
    width: 60%;
    height: 600px;
    margin: 0 auto;
  }
  </style>