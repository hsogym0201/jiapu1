<template>
  <div id="app">
    <h1>寻根问祖，探索家族的历史脉络</h1>
    <h3>每个姓氏背后都有一段深厚的历史与文化...</h3>

    <div class="navbar">
      <div class="navbar-title">
        寻根问祖 <span>可以看见的家谱可视化平台</span>
      </div>
      <div class="navbar-menu">
        <a
            v-for="(item, index) in menuItems"
            :key="index"
            href="#"
            :class="['menu-item', { active: activeIndex === index }]"
            @click="setActive(index)"
        >
          {{ item }}
        </a>
      </div>
    </div>

    <h4>家谱地图展示--了解你的姓氏在全国各地的家族故事</h4>
    <div class="horizontal-container">
      <XingShiXuanZe />
      <DiTu1 />
    </div>

    <hr />
    <h4>地域姓氏构成对比--探索各地的家族足迹</h4>
    <div class="horizontal-container">
      <CiYun :wordArr="words" @word-clicked="handleWordClick" />
      <DiTu2 @province-clicked="handleProvinceClick" />
    </div>

    <hr />
    <h4>姓氏时空上的分布趋势--深入家族传承之路与繁衍之势</h4>
    <div class="horizontal-container">
      <ZheXianTu ref="zheXianTu" :versionData="versionData" :chartTitle="chartTitle" />
      <ZhuZhuangTu ref="zhuZhuangTu" :versionTypeData="versionTypeData" :ChartTitle="ChartTitle" />
    </div>
  </div>
</template>

<script>
import XingShiXuanZe from './components/XingShiXuanZe.vue';
import DiTu1 from './components/DiTu1.vue';
import CiYun from '@/components/CiYun.vue';
import DiTu2 from '@/components/DiTu2.vue';
import ZheXianTu from '@/components/ZheXianTu.vue';
import ZhuZhuangTu from '@/components/ZhuZhuangTu.vue';

const jsonData = require('./assets/new.json');

export default {
  name: 'App',
  components: {
    XingShiXuanZe,
    DiTu1,
    CiYun,
    DiTu2,
    ZheXianTu,
    ZhuZhuangTu,
  },
  data() {
    return {
      menuItems: ['家谱地图展示', '地域姓氏构成对比', '姓氏时空上的分布趋势'],
      activeIndex: 0,
      words: [],
      versionData: [],//折线图版本数据
      versionTypeData: [],//柱状图版本类型数据
      chartTitle: '浙江省李姓家谱数量变化',  // 折线图默认标题
      ChartTitle: '浙江省李姓家谱版本类型统计',
      clickedWord: null,
      showPopup: false,
      selectedProvince: '浙江', // 默认选中省份
      defaultSurname: '李', // 默认姓氏
    };
  },
  methods: {
    setActive(index) {
      this.activeIndex = index;
    },

    // 获取选中省份的前20个姓氏
    getTop20Surnames(province) {
      const surnameCounts = {};
      jsonData.forEach((item) => {
        if (item.居地 === province) {
          surnameCounts[item.姓氏] = (surnameCounts[item.姓氏] || 0) + 1;
        }
      });

      const sortedSurnames = Object.entries(surnameCounts).sort((a, b) => b[1] - a[1]);
      return sortedSurnames.map(([name]) => ({ name, light: true }));
    },

    // 获取指定姓氏的版本年代数据
    getSurnameVersionData(surname) {
      const versionCounts = {};

      jsonData.forEach(item => {
        if (item.姓氏 === surname && item.居地 === this.selectedProvince) {
          const version = item.版本年代;
          versionCounts[version] = (versionCounts[version] || 0) + 1;
        }
      });

      const versionData = Object.entries(versionCounts).map(([version, count]) => ({
        version,
        count,
      }));

      versionData.sort((a, b) => a.version - b.version);
      return versionData;
    },

    getVersionTypeData(surname) {
      const versionTypeCounts = {};

      jsonData.forEach(item => {
        if (item.姓氏 === surname && item.居地 === this.selectedProvince) {
          const version = item.版本类型;
          versionTypeCounts[version] = (versionTypeCounts[version] || 0) + 1;
        }
      });

      const  versionTypeData = Object.entries(versionTypeCounts).map(([version, count]) => ({
        version,
        count,
      }));

      versionTypeData.sort((a, b) => a.version - b.version);
      return versionTypeData;
    },

    // 处理词云点击事件，显示该姓氏的版本年代数据
    handleWordClick(word) {
      const surname = word.name;
      this.versionData = this.getSurnameVersionData(surname);
      this.chartTitle = `${this.selectedProvince}省${surname}姓家谱数量变化`;  // 动态更新标题
      this.updateLineChart(this.versionData); // 更新折线图

      this.versionTypeData = this.getVersionTypeData(surname); // 获取该姓氏的版本类型数据
      this.ChartTitle = `${this.selectedProvince}省${surname}姓家谱版本类型统计`;  // 动态更新标题
      this.updatePieChart(this.versionTypeData); // 调用饼图更新方法
    },

    // 更新折线图
    updateLineChart(versionData) {
      this.$refs.zheXianTu.updateChart(versionData);
    },
    // 更新饼图
    updatePieChart(versionTypeData) {
      this.$refs.zhuZhuangTu.updateChart(versionTypeData); // 通过ref更新柱状图组件的数据
    },

    // 处理地图点击事件，更新当前选中的省份
    handleProvinceClick(province) {
      this.selectedProvince = province;
      this.words = this.getTop20Surnames(province);  // 更新词云数据

      this.versionData = this.getSurnameVersionData(this.defaultSurname);  // 获取默认“李”姓数据
      this.chartTitle = `${province}省${this.defaultSurname}姓家谱数量变化`;  // 更新标题

      this.versionTypeData = this.getVersionTypeData(this.defaultSurname);
      this.ChartTitle = `${province}省${this.defaultSurname}姓家谱版本类型统计`;
    },

    closePopup() {
      this.showPopup = false;
    },
  },
  mounted() {
    this.words = this.getTop20Surnames(this.selectedProvince);  // 初始化词云数据
    this.versionData = this.getSurnameVersionData(this.defaultSurname);  // 默认显示“李”姓数据
    this.versionTypeData = this.getVersionTypeData(this.defaultSurname);
  },
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
h1 {
  font-family: 'Georgia', serif;
  font-size: 2.5rem;
  color: #2c3e50;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  margin-bottom: 20px;
}
h3 {
  font-family: 'Arial', sans-serif;
  font-size: 1.2rem;
  color: #34495e;
  line-height: 1.8;
  max-width: 800px;
  margin: 0 auto;
  text-align: justify;
}
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 20px;
  background-color: #fff;
  border-bottom: 1px solid #ddd;
  font-family: 'Arial', sans-serif;
  margin-left: 200px;
  border: none;
}
.navbar-title {
  font-size: 1.5rem;
  font-weight: bold;
  color: #000;
}
.navbar-menu {
  display: flex;
  gap: 20px;
  margin-right: 200px;
}
.menu-item {
  text-decoration: none;
  color: #333;
  font-size: 1rem;
  padding: 5px 10px;
  position: relative;
  transition: color 0.3s ease;
}
.menu-item:hover {
  color: #f60;
}
.menu-item.active {
  color: #f60;
  font-weight: bold;
}
.horizontal-container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 30px;
  padding: 10px;
}
</style>
