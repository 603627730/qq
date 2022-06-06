<template>
  <div>
    <input v-model="message" style="text-align: left"  placeholder="xx市或者xx区，例如：城阳区">
    <button v-on:click="getinfo()">查询</button>
    <p>查询时间 ： {{timeis}}</p>

  </div>
  <div
      class="echart"
      id="mychart"
      :style="{ float: 'left', width: '30%', height: '400px' }"
  >

  </div>
</template>


<script>

import * as echarts from "echarts";
import axios from 'axios'

export default {
  data() {
    return {
      message : '城阳区',
      input: '',
      timeis : '',
      name: "张雪",
      xData: ["2020-02", "2020-03", "2020-04", "2020-05"], //横坐标数据
      yData: [120, 132], //纵坐标数据，与横坐标对应
      ainfo :[]
    };
  },
  mounted() {

    this.getinfo()
  },
  methods: {

    getinfo(){
      axios
          .get('http://139.196.6.43:8000/file/readExcel?city=' + this.message)
          .then(response => (
              this.ainfo = response.data,
                  console.log(this.ainfo.temperature),
                  this.initEcharts(),
                  this.timeis = this.ainfo.reporttime
          ))
    },

    initEcharts() {
      console.log("this.ainfo.temperature")

      const option = {
        title: {
          text: this.ainfo.province + "省" + this.ainfo.city + "天气情况"
        },
        tooltip: {},
        legend: {
          data: [ "天气状况"]
        },
        xAxis: {
          data: [ "温度","湿度"]
        },
        yAxis: {},
        series: [
          {
            name: "天气状况",
            type: "bar", //类型为柱状图
            data: [parseInt(this.ainfo.temperature), parseInt(this.ainfo.humidity)]
          }
        ]
      };
      const myChart = echarts.init(document.getElementById("mychart"));// 图标初始化
      myChart.setOption(option);// 渲染页面
      //随着屏幕大小调节图表
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    }
  }
};
</script>


