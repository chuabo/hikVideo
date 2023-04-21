<template>
  <div class="container">
    <hk-video ref="hkvideo" :argument="argument" @windowChang="onWindChange"   :playMode="playMode"></hk-video>
    <div class="opt-view">
      <button class="btn" @click="preview">预览2</button>
      <button class="btn" @click="stop">停止所有预览</button>
      <button class="btn" @click="setLayout('1x1')">1x1</button>
      <button class="btn" @click="setLayout('2x2')">2x2</button>
      <button class="btn" @click="setLayout('3x3')">3x3</button>
      <button class="btn" @click="setLayout('4x4')">4x4</button>
      <button class="btn" @click="stopList">停止当前窗口预览</button>
      <button class="btn" @click="changeMode">切换模式</button>
      <button class="btn" @click="startPlayBack">回放</button>
      <button class="btn" @click="drawText">叠加文字</button>
      <button class="btn" @click="setFullScreen">进入全屏</button>
      <button class="btn" @click="exitFullScreen">退出全屏</button>
    </div>
  </div>
</template>

<script>
import hkVideo from "hkVideo/index.vue";
export default {
  data() {
    return {
      argument: {
        appkey: "23763863", //API网关提供的appkey
        secret: "4VsGoXaY2ssQDvydMy20", //API网关提供的secret
        ip: "122.9.138.69", //API网关IP地址
        port: 443,
        windId:1,//当前窗口号
      },
      //cameraIndexCode:'cb50afe816b24759b7347aa2e0c4c0fe',
      cameraIndexCode: 'cb50afe816b24759b7347aa2e0c4c0fe',
      playMode:0
    }
  },
  components: {
    hkVideo,
  },
  methods: {
  //预览
    preview() {
    debugger
      this.$refs.hkvideo.startPreview(this.cameraIndexCode);
    },
    //停止预览
    stop() {
      this.$refs.hkvideo.stopAllPreview();
    },
    //设置布局
    setLayout(layout){
         this.$refs.hkvideo.setLayout(layout);
    },
    //窗口改变监听
    onWindChange(windId){
       this.windId=windId
    },
    //批量停止预览
    stopList(){
      this.$refs.hkvideo.stopListPreview([this.windId])   
    },
    //回放
    startPlayBack(){
        this.$refs.hkvideo.startPlayBack(this.cameraIndexCode,'2022-07-01 9:00','2022-07-27 20:00')
    },
    //切换模式
    changeMode(){
        this.playMode=this.playMode ? 0:1
    },
    //画面字符叠加
    drawText(){
       this.$refs.hkvideo.drawText('叠加文字测试')
    },
    //全屏
    setFullScreen(){
      this.$refs.hkvideo.setFullScreen()
    },
    //退出全屏
    exitFullScreen(){
      this.$refs.hkvideo.exitFullScreen()
    }
  }
}