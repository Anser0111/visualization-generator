<template>
  <div id="home">
    <h1>{{ title }}</h1>
    <div id="main" style="width: 600px; height: 400px"></div>
    <hr />
    <div id="input">
      <div id="input1">
        <div>{{ div1 }}</div>
        <input type="text" v-model="count" />
      </div>
      <div id="input2">
        <div>{{ div2 }}</div>
        <input type="text" v-model="second" />
      </div>
      <div id="input3">
        <div>{{ div3 }}</div>
        <div v-for="(item, index) in parseInt(count)" :key="index">
          <span>{{ item + ". " }}</span>
          <input type="text" v-model="nameArr[index]" />
          <input type="text" v-model="initArr[index]" />
          <input type="text" v-model="endArr[index]" />
        </div>
      </div>
    </div>
    <div id="start">
      <button @click="animation">开始</button>
      <button @click="random">随机数据测试</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      title: "数据可视化生成器",
      div1: "请输入将要输入数据的组数：",
      count: 4,
      div2: "请输入动画持续的时长（秒）：",
      second: 5,
      div3: "请输入每组数据的名称，初始值，结束值：",
      nameArr: [],
      initArr: [],
      endArr: [],
      myChart: {},
      option: {},
      data: [], //要放数据的位置
      flag: false,
      color: [],
    };
  },
  methods: {
    initChart() {
      // 基于准备好的dom，初始化echarts实例
      this.myChart = this.$echarts.init(document.getElementById("main"));
      // 指定图表的配置项和数据
      this.option = {
        xAxis: {
          max: "dataMax",
        },
        yAxis: {
          type: "category",
          data: this.nameArr,
          inverse: true,
          animationDuration: 300,
          animationDurationUpdate: 300,
          max: 4, // only the largest 5 bars will be displayed
        },
        series: [
          {
            realtimeSort: true,
            name: "X",
            type: "bar",
            data: this.data,
            label: {
              show: true,
              position: "right",
              valueAnimation: true,
            },
          },
        ],
        legend: {
          show: false, // 是否显示图例
        },
        animationDuration: 0, // 初始动画的时长
        animationDurationUpdate: 3000, // 数据更新动画的时长
        animationEasing: "linear",
        animationEasingUpdate: "linear",
      };
    },
    run() {
      // 如果已经建立了echarts实例，则销毁。
      if (this.flag) {
        this.myChart.dispose();
      }
      this.data = this.initArr.map((item) => +item);
      this.initChart();
      this.myChart.setOption(this.option);
      let data = this.option.series[0].data;
      for (let i = 0; i < data.length; ++i) {
        data[i] = parseInt(this.endArr[i]);
      }
      this.option.yAxis.max = this.count - 1;
      this.option.animationDurationUpdate = this.second * 1000;
      this.myChart.setOption(this.option);
      this.flag = true;
    },
    animation() {
      setTimeout(() => {
        this.run();
      }, 0);
    },
    random() {
      for (let i = 0; i < this.count; i++) {
        this.nameArr[i] = Math.round(Math.random() * 100);
        this.initArr[i] = Math.round(Math.random() * 2000);
        this.endArr[i] = Math.round(Math.random() * 5000);
      }
      this.run();
      for (let i = 0; i < this.count; i++) {
        this.nameArr[i] = null;
        this.initArr[i] = null;
        this.endArr[i] = null;
      }
    },
  },
  mounted() {
    this.random();
  },
};
</script>

<style>
button {
  margin: 5px;
}
input {
  margin-right: 5px;
  margin-bottom: 2px;
}
</style>
