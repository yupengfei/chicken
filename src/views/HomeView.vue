<style scoped>
#upload {
  position: fixed;
  width: 360px;
  height: 60px;
  font-size: 20px;
  top: 0%;
  left: 50%;
  margin-top: 0px;
  /* Negative half of height. */
  margin-left: -180px;
  /* Negative half of width. */
}
</style>
<template>
  <!-- <input type="file" @change="onFileChanged($event)"
    accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" ref="file" /> -->
  <div id="upload">
    <input type="file" @change="onFileChanged($event)" accept="application/vnd.ms-excel" />
    <br>
    <label>{{ info }}</label>
  </div>

  <div id="chart" style="width: 100%;height:100%;"></div>

</template>

<script setup lang="ts">
import { read, utils } from 'xlsx';
import { ref } from 'vue';

const info = ref('')

// 异步读取，原理未知
// https://stackoverflow.com/questions/39366810/how-can-i-read-a-file-from-an-html-input-with-js-xlsx
const onFileChanged = async (event: Event) => {

  const target = event.target as HTMLInputElement;
  if (target && target.files) {
    let file = target.files[0];
    console.log(file)
    info.value = "正在加载" + file.name + "，需要十几秒"
    let workbook = read(await file.arrayBuffer(), { type: 'binary' })
    console.log(workbook.SheetNames[0])
    let first_ws = workbook.Sheets[workbook.SheetNames[0]];

    let data = utils.sheet_to_json(first_ws)
    console.log(data)
    info.value = "加载完成" + file.name
  }
}
</script>