<style scoped>
#chart {
  width: 100vw;
  height: 90vh;
}

#upload {
  position: fixed;
  width: 660px;
  height: 60px;
  /* font-size: 20px; */
  top: 0%;
  left: 50%;
  margin-top: 0px;
  /* Negative half of height. */
  margin-left: -330px;
  /* Negative half of width. */
}
</style>
<template>
  <!-- <input type="file" @change="onFileChanged($event)"
    accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" ref="file" /> -->
  <div id="upload">
    <input type="file" @change="onFileChanged($event)" accept="application/vnd.ms-excel" /> <label>{{ info }}</label>
  </div>

  <div id="chart"></div>

</template>

<script setup lang="ts">
import { read, utils } from 'xlsx';
import { ref, onMounted } from 'vue';
import * as echarts from 'echarts';
// type EChartsOption = echarts.EChartsOption;
// 天数 室内平均温度 室外温度 室内湿度 室外湿度 目标温度 静压 小窗1开度 进风口1开度 通风开时长 通风关时长 耗水量
type Point = {
  记录时间: string,
  天数: number,
  室内平均温度: number,
  室外温度: number;
  室内湿度: number;
  室外湿度: number;
  目标温度: number;
  静压: number;
  小窗1开度: number,
  进风口1开度: number,
  通风开时长: number,
  通风关时长: number,
  耗水量: number,
}

const info = ref('')

// 异步读取，原理未知
// https://stackoverflow.com/questions/39366810/how-can-i-read-a-file-from-an-html-input-with-js-xlsx
const onFileChanged = async (event: Event) => {

  const target = event.target as HTMLInputElement;
  if (target && target.files) {
    let file = target.files[0];
    console.log(file)
    info.value = "正在加载" + file.name + "，需要十几秒"
    blank()
    let workbook = read(await file.arrayBuffer(), { type: 'binary' })
    console.log(workbook.SheetNames[0])
    let first_ws = workbook.Sheets[workbook.SheetNames[0]];

    let data = utils.sheet_to_json<Point>(first_ws)
    console.log(data)
    info.value = "加载完成" + file.name
    drawData(data)
  }
}

onMounted(() => {
  // drawData([])
})

const blank = () => {
  var chartDom = document.getElementById('chart')!;
  echarts.dispose(chartDom);

}

const drawData = (data: Point[]) => {
  var chartDom = document.getElementById('chart')!;
  var myChart = echarts.init(chartDom);
  var option: echarts.EChartsOption;

  option = {
    xAxis: {
      type: 'category',
      boundaryGap: false,
      data: data.map(d => d.记录时间)
    },
    yAxis: [{
      type: 'value',
      position: 'left',
    },
    {
      type: 'value',
      position: 'right',
    }
    ],
    tooltip: {
      trigger: 'axis',
      // axisPointer: {
      //   type: 'cross'
      // },
      backgroundColor: 'rgba(255, 255, 255, 0.8)',
      extraCssText: 'width: 200px'
    },
    dataZoom: [
      {
        type: 'inside',
        start: 0,
        end: 10
      },
      {
        start: 0,
        end: 10
      }
    ],
    grid: {
      left: "60px",
      right: "60px",
    },
    // legend: {
    //   type: 'scroll',
    //   orient: 'vertical',
    //   right: 10,
    //   top: 20,
    //   bottom: 20,
    // },
    legend: {
      left: 'left'
    },
    // 室外湿度: number;
    // 目标温度: number;
    // 静压: number;
    // 小窗1开度: number,
    // 进风口1开度: number,
    // 通风开时长: number,
    // 通风关时长: number,
    // 耗水量: number,
    series: [
      {
        name: "天数",
        type: 'line',
        data: data.map(d => d.天数),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "室内平均温度",
        type: 'line',
        data: data.map(d => d.室内平均温度),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "室外温度",
        type: 'line',
        data: data.map(d => d.室外温度),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "室内湿度",
        type: 'line',
        data: data.map(d => d.室内湿度),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "室外湿度",
        type: 'line',
        data: data.map(d => d.室外湿度),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "目标温度",
        type: 'line',
        data: data.map(d => d.目标温度),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "静压",
        type: 'line',
        data: data.map(d => d.静压),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "小窗1开度",
        type: 'line',
        data: data.map(d => d.小窗1开度),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "进风口1开度",
        type: 'line',
        data: data.map(d => d.进风口1开度),
        yAxisIndex: 0 // 使用第一个Y轴
      },
      {
        name: "通风开时长",
        type: 'line',
        data: data.map(d => d.通风开时长),
        yAxisIndex: 1 // 使用第一个Y轴
      },
      {
        name: "通风关时长",
        type: 'line',
        data: data.map(d => d.通风关时长),
        yAxisIndex: 1 // 使用第一个Y轴
      },
      {
        name: "耗水量",
        type: 'line',
        data: data.map(d => d.耗水量),
        yAxisIndex: 1 // 使用第一个Y轴
      },
    ]
  };

  option && myChart.setOption(option);
}
</script>