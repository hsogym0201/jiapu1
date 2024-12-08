<template>
  <div id="china-map" style="width: 100%; height: 500px;"></div>
</template>

<script>
import { defineComponent, onMounted } from "vue";
import * as echarts from "echarts/core";
import { use, registerMap } from "echarts/core";
import { MapChart } from "echarts/charts";
import { TooltipComponent } from "echarts/components";
import { CanvasRenderer } from "echarts/renderers";
import chinaMap from "@/assets/china.json"; // 中国地图数据

use([CanvasRenderer, MapChart, TooltipComponent]);

export default defineComponent({
  name: "chinaMap",
  emits: ["province-clicked"], // 声明事件
  setup(props, { emit }) {
    onMounted(() => {
      const chart = echarts.init(document.getElementById("china-map"));

      // 注册地图数据
      registerMap("china", chinaMap);

      // 配置地图
      const option = {
        tooltip: {
          trigger: "item",
          formatter: (params) => {
            return params.name; // 仅显示省份名称
          },
        },
        series: [
          {
            name: "中国地图",
            type: "map",
            map: "china",
            roam: true, // 支持缩放和拖动
            label: {
              show: true, // 显示省份名称
              color: "#000", // 名称字体颜色
              fontSize: 12, // 名称字体大小
              emphasis: {
            },
              label: {
                show: true,
                color: "red", // 点击后高亮显示文字颜色
              },
              itemStyle: {
                areaColor: "yellow", // 点击后高亮区域颜色
              },
            },
            data: [], // 不需要显示统计数据，因此数据为空
          },
        ],
      };

      chart.setOption(option);

      // 监听点击事件
      chart.on("click", (params) => {
        if (params.componentType === "series") {

          const provinceName = params.name; // 获取被点击的省份名称
          console.log("点击的省份：", provinceName);
          emit("province-clicked", provinceName); // 通过事件将省份名称传递给父组件
        }
      });
    });
  },
});
</script>

<style>
/* 添加样式（可选） */
</style>
