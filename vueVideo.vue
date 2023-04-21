<template>
  <div>
    <video :id="'myVideo' + deviceSerial" class="video-js vjs-default-skin vjs-big-play-centered" muted="muted" controls preload="auto" style="width: 600px;height: 400px"></video>
  </div>
</template>

<script>

import "videojs-contrib-hls"
import videojs from "video.js";

export default {
  name: "VueVideo",
  props: {
    deviceSerial: {
      type: String,
      default: ''
    },
    url: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      player: null,
      src: ''
    }
  },
  watch: {
    url: {
      handler(val) {
        if (val) {
          this.init(val);
        }
      },
      immediate: true
    }
  },
  mounted() {
  },
  methods: {
    init(url) {
      const self = this;

      if (this.player) {
        this.player.src({
          src: url,
          type: "application/x-mpegURL"
        })
        this.player.load();
        this.player.play();
      }

      if (!this.player) {
        let options = {
          bitPlayButton: true,
          autoplay: true, // 设置自动播放
          controls: true, // 显示播放的控件
          fluid: true,
          sources: [
            //如果需要切换视频源，src需要动态设置
            {
              src: url,
              type: "application/x-mpegURL", // 告诉videojs,这是一个m3u8流
            }
          ]
        }
        setTimeout(() => {
          self.player = videojs('myVideo' + this.deviceSerial, options, function onPlayerReady() {
            videojs.log('Your player is ready!');

            /*this.on("loadstart",function(){
              console.log("开始请求数据 ");
            })
            this.on("progress",function(){
              console.log("正在请求数据 ");
            })
            this.on("loadedmetadata",function(){
              console.log("获取资源长度完成 ")
            })
            this.on("canplaythrough",function(){
              console.log("视频源数据加载完成")
            })
            this.on("waiting", function(){
              console.log("等待数据")
            });
            this.on("play", function(){
              console.log("视频开始播放")
            });
            this.on("playing", function(){
              console.log("视频播放中")
            });
            this.on("pause", function(){
              console.log("视频暂停播放")
            });
            this.on("ended", function(){
              console.log("视频播放结束");
            });
            this.on("error", function(){
              console.log("加载错误")
            });
            this.on("seeking",function(){
              console.log("视频跳转中");
            })
            this.on("seeked",function(){
              console.log("视频跳转结束");
            })
            this.on("ratechange", function(){
              console.log("播放速率改变")
            });
            this.on("timeupdate",function(){
              console.log("播放时长改变");
            })
            this.on("volumechange",function(){
              console.log("音量改变");
            })
            this.on("stalled",function(){
              console.log("网速异常");
            })*/
          });
        },300)
      }
    },
    stop() {
      if (!this.player) return;
      this.player.off();
    }
  },
  beforeDestroy(){
    this.player.dispose();
  }
};
</script>
