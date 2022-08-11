<!-- page1 -->
<script setup>
import { ref,onUnmounted,onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { storeToRefs } from "pinia";
import {useMainStore } from "@/store/store.js";
import to from 'await-to-js';
import { Toast,Uploader } from 'vant';
import Tesseract from 'tesseract.js';

let fileList = ref([]);
let infoData =ref('')
const worker = Tesseract.createWorker();

onMounted(async()=>{
  await worker.load();
  //识别语言文档https://tesseract-ocr.github.io/tessdoc/Data-Files#data-files-for-version-400-november-29-2016
  await worker.loadLanguage('eng+chi_sim');
  await worker.initialize('eng+chi_sim');
  await worker.setParameters({
    // tessedit_char_whitelist: '0123456789',//设置规则，只识别数字
  });
})

const discernImg = async(imgUrl)=>{
  const [err,data] = await to(worker.recognize(imgUrl));
  console.log(err,data)
  if(err){
    Toast.clear();
    Toast('识别失败');
  }else{
    let hocr = data.data.hocr;
    infoData.value = hocr;
    Toast.clear();
  }  
}

const afterRead = (result) => {
  //bese64
  // console.log(result)
  Toast.loading({
    message: '识别加载中...',
    forbidClick: true,
    duration: 0,
  });
  discernImg(result.content)
}



// 生命周期-卸载或者隐藏
onUnmounted(() => {
  worker.terminate();
})

</script>

<template>
  <div class="page1">
    <div class="main">
      <Uploader v-model="fileList" :after-read="afterRead" :max-count="1"></Uploader>
    </div>
    <div class="info">
      <div class="info-header">识别内容出来的内容</div>
      <div class="info-box" v-html="infoData"></div>
    </div>
  </div>
</template>

<style lang='scss'>
.page1{
  // font-size: $fontSize;
  // @include gSetLine();
  // text-align: center;
  padding-top: 30px;
  width: 1200px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  .main{
    width: 600px;
    height: 400px;
    .van-uploader{
      width: 100%;
      height: 100%;
      div{
        width: 100%;
        height: 100%;
      }
    }
  }
  .info{
    width: 500px;
    .info-header{
      width: 100%;
      height: 60px;
      font-size: 24px;
      text-align: center;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .info-box{
      width: 100%;
      height: 340px;
      background: #f7f8fa;
      color: #000;
      font-size: 24px;
    }
  }
}
</style>