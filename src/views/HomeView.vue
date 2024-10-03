<template>
  <!-- <input type="file" @change="onFileChanged($event)"
    accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" ref="file" /> -->
  <input type="file" @change="onFileChanged($event)" accept="application/vnd.ms-excel" />
</template>

<script setup lang="ts">
import { read, utils } from 'xlsx';

// 异步读取，原理未知
// https://stackoverflow.com/questions/39366810/how-can-i-read-a-file-from-an-html-input-with-js-xlsx
const onFileChanged = async (event: Event) => {

  const target = event.target as HTMLInputElement;
  if (target && target.files) {
    let file = target.files[0];
    console.log(file)
    let workbook = read(await file.arrayBuffer(), { type: 'binary' })
    console.log(workbook.SheetNames[0])
    let first_ws = workbook.Sheets[workbook.SheetNames[0]];
    // let aaa = first_ws[1]
    // console.log(aaa)
    let data = utils.sheet_to_json(first_ws)
    console.log(data)
  }
}
</script>