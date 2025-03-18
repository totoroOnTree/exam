<template>
  <!-- 显示读取到的数据 -->
  <div class="list">
    <div class="li" v-if="currentQuestion">
      <div class="title">
        <span>{{currentQuestion[0]}}、</span>{{currentQuestion[1] || ''}}
      </div>
      <div class="choose">
        <el-checkbox-group v-model="currentChoose">
          <div><el-checkbox :label="`A.${currentQuestion[3] || ''}`" value="A" /></div>
          <div><el-checkbox :label="`B.${currentQuestion[4] || ''}`" value="B" /></div>
          <div><el-checkbox :label="`C.${currentQuestion[5] || ''}`" value="C" /></div>
          <div><el-checkbox :label="`D.${currentQuestion[6] || ''}`" value="D" /></div>
        </el-checkbox-group>
      </div>
      <div style="margin: 10px 0 15px 15px;">
        正确答案：<button @click="onLook" v-show="!showRight">查看</button><span style="display: none;color: red;font-size: 16px;" v-show="showRight">{{currentQuestion[2]}}</span>
      </div>
    </div>
    <el-button type="primary" @click="onHandleNext">下一题</el-button>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import * as XLSX from 'xlsx';

// 定义存储 Excel 数据的响应式变量
const data = ref(null);
const currentChoose = ref([]);
const currentNum = ref(0);
const isShowRight = ref(false);

const currentQuestion = computed(() => {
  return data.value ? data.value[currentNum.value] : null
});
const showRight = computed(() => {
  return isShowRight.value || currentChoose.value.length
});

function isStringifiedNumber(value) {
    if (typeof value !== 'string') return false; // 首先检查是否为字符串
    const num = Number(value);
    return !isNaN(num) && isFinite(num); // 排除Infinity和NaN的情况
}

function onLook(){
  isShowRight.value = true
}

function onHandleNext(){
  currentChoose.value = []
  isShowRight.value = false
  if (currentNum.value >= data.value.length) {
    currentNum.value = 0
    return
  }

  ++currentNum.value;
  console.log(currentNum.value, 'currentNum');
}

onMounted(async () => {
  try {
    // 假设 Excel 文件名为 sample.xlsx，放置在 public 目录下
    const response = await fetch('/questions.xlsx');
    const arrayBuffer = await response.arrayBuffer();
    const workbook = XLSX.read(new Uint8Array(arrayBuffer), { type: 'array' });
    console.log(workbook)
    const firstSheetName = workbook.SheetNames[0];
    const worksheet = workbook.Sheets[firstSheetName];
    
    const jsonData = XLSX.utils.sheet_to_json(worksheet, {header: 1,range:'A1:H1552'});
    data.value = jsonData.filter(it=>it[0] && isStringifiedNumber(it[0]));
    console.log(data.value)
  } catch (error) {
    console.error('读取 Excel 文件时出错:', error);
  }
});
</script>

<style scoped>
html,body{
  text-align: left;
}
.li{
  margin-top: 30px;
}
.choose{
  margin-top: 10px;
  margin-left: 15px;
}
.choose div{
  margin: 5px 0;
}
</style>
