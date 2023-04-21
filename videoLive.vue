<template>
  <div class="video-class">
    <div id="myPlayer" style="width:600px;height:400px"></div>
  </div>
</template>

<script>
import EZUIKit from "ezuikit-js";
import qs from 'qs';

export default {
  name: "VideoLive",
  props: {
  },
  data() {
    return {
      player: null,
      accessToken: '',
      deviceSerial: '',
      template: 'simple'
    }
  },
  watch: {
  },
  created() {
    const location = window.location;
    const params = qs.parse(location.hash.split('?')[1]);
    this.accessToken = params.accessToken;
    this.deviceSerial = params.deviceSerial;
    this.init();
  },
  mounted() {
  },
  methods: {
    init() {
      this.player =  new EZUIKit.EZUIKitPlayer({
        autoplay: true,
        id: "myPlayer",
        accessToken: this.accessToken,
        url: "ezopen://open.ys7.com/" + this.deviceSerial + "/1.live",
        template: this.template, // simple - 极简版;standard-标准版;security - 安防版(预览回放);voice-语音版；
        // 视频上方头部控件
        //header: ["capturePicture", "save", "zoom"], // 如果templete参数不为simple,该字段将被覆盖
        //plugin: ['talk'],                       // 加载插件，talk-对讲
        // 视频下方底部控件
        //footer: ["talk", "broadcast", "hd", "fullScreen"], // 如果template参数不为simple,该字段将被覆盖
        // audio: 1, // 是否默认开启声音 0 - 关闭 1 - 开启
        // openSoundCallBack: data => console.log("开启声音回调", data),
        // closeSoundCallBack: data => console.log("关闭声音回调", data),
        // startSaveCallBack: data => console.log("开始录像回调", data),
        // stopSaveCallBack: data => console.log("录像回调", data),
        // capturePictureCallBack: data => console.log("截图成功回调", data),
        // fullScreenCallBack: data => console.log("全屏回调", data),
        // getOSDTimeCallBack: data => console.log("获取OSDTime回调", data),
        width: 600,
        height: 400
      });
      //setTimeout(() => {
      //  this.player.play();
      //}, 200);
    },
    stop() {
      if (!this.player) return;
      this.player.stop();
      document.getElementById('myPlayer').innerHTML = '';
    },
    beforeDestroy(){
      if (!this.player) return;
      this.player.stop();//关闭视频流
      document.getElementById('myPlayer').innerHTML = '';
    }
  }
};
</script>

<style lang="scss" scoped>
.video-class {
  width: 100%;
  height: 100%;
}
</style>
