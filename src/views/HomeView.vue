<template>
  <input type="file" @change="onFileChanged($event)"
    accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" ref="file" />
</template>

<script setup lang="ts">
import { read } from 'xlsx';
import { ref } from "vue"

const file = ref(null)

// 异步读取，原理未知
// https://stackoverflow.com/questions/39366810/how-can-i-read-a-file-from-an-html-input-with-js-xlsx
const onFileChanged = async (event: Event) => {

  const target = event.target as HTMLInputElement;
  if (target && target.files) {
    let file = target.files[0];
    console.log(file)
    let wb = read(await file.arrayBuffer(), { type: 'binary' })
    console.log(wb.SheetNames[0])
  }
}
</script>