<template>
  <div :title="title" :id="id" :style="{width: swfWidth+'px',height: swfHeight+'px'}" class="showvideo">
    <el-dialog v-if="downloadDialog" title="提示" :visible.sync="dialogVisible" width="30%">
      <span>{{downloadText}}</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="handleDownloadExe">下载</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
export default {
  props: {
    title: {
      default: ''
    },
    id: {
      default: 'playWnd'
    },
    swfWidth: {
      default: () => 600
    },
    swfHeight: {
      default: () => 400
    },
    layout: {
      default: '1x1'
    },
    autoPlay: {
      default: true
    },
    cameraIndexCode: {
      default: ''
    },
    //海康插件下载url
    downloadUrl: {
      type: String,
      default: "/upload/VideoWebPlugin.exe",
    },
    downloadText:{
      type:String,
      default:'插件启动失败，请检查插件是否安装,如果未安装请点击下载安装,安装后刷新页面'
    }
  },
  data() {
    return {
      oWebControl: undefined,
      initCount: 0,
      pubKey: "",
      dialogVisible: false,
    }
  },
  watch: {
    cameraIndexCode: {
      handler(val) {
        if (val) {
          this.initPlugin(this.cameraIndexCode);
        }
      },
      immediate: true
    }
  },
  created() {
    //this.initPlugin(this.cameraIndexCode);
  },
  mounted() {
    let that = this;
    this.observeWrapper();
    window.addEventListener('resize', () => {
      if (this.oWebControl != null) {
        this.oWebControl.JS_Resize(600, 400);
        this.setWndCover();
      }
    });
    window.addEventListener('scroll', () => {
      if (this.oWebControl != null) {
        this.oWebControl.JS_Resize(600, 400);
        this.setWndCover();
      }
    });
  },

  destroyed() {
    if (this.oWebControl != null) {
      this.oWebControl.JS_HideWnd();
      this.oWebControl.JS_Disconnect().then(
          function () {
          },
          function () {
          }
      );
    }
  },
  methods: {
    close() {
      this.$parent.show_playShipin = false;
      if (this.oWebControl != null) {
        this.oWebControl.JS_HideWnd();
        this.oWebControl.JS_Disconnect().then(
            function () {
            },
            function () {
            }
        );
      }
    },
    //推送消息
    cbIntegrationCallBack(oData) {
      //showCBInfo(JSON.stringify(oData.responseMsg));
    },
    //监听自身容器大小变化
    observeWrapper() {
      const ro = new ResizeObserver((entries) => {
        for (const entry of entries) {
          const cr = entry.contentRect;
          this.videoWidth = cr.width;
          this.videoHeight = cr.height;
          this.oWebControl &&
          this.oWebControl.JS_Resize(this.videoWidth, this.videoHeight);
          this.oWebControl && this.setWndCover();
        }
      });
      ro.observe(document.querySelector(`#${this.id}`));
    },
    setWndCover() {
      //裁剪插件实例的大小
      let iWidth = document.body.clientWidth;
      let iHeight = document.body.clientHeight;
      let oDivRect = document.getElementById(this.id).getBoundingClientRect();

      let iCoverLeft = oDivRect.left < 0 ? Math.abs(oDivRect.left) : 0;
      let iCoverTop = oDivRect.top < 0 ? Math.abs(oDivRect.top) : 0;
      let iCoverRight =
          oDivRect.right - iWidth > 0 ? Math.round(oDivRect.right - iWidth) : 0;
      let iCoverBottom =
          oDivRect.bottom - iHeight > 0
              ? Math.round(oDivRect.bottom - iHeight)
              : 0;
      iCoverLeft = iCoverLeft > this.videoWidth ? this.videoWidth : iCoverLeft;
      iCoverTop = iCoverTop > this.videoHeight ? this.videoHeight : iCoverTop;
      iCoverRight =
          iCoverRight > this.videoWidth ? this.videoWidth : iCoverRight;
      iCoverBottom =
          iCoverBottom > this.videoHeight ? this.videoHeight : iCoverBottom;

      this.oWebControl.JS_RepairPartWindow(0, 0, 1001, 600);
      if (iCoverLeft != 0) {
        this.oWebControl.JS_CuttingPartWindow(0, 0, iCoverLeft, 600);
      }
      if (iCoverTop != 0) {
        this.oWebControl.JS_CuttingPartWindow(0, 0, 1001, iCoverTop);
      }
      if (iCoverRight != 0) {
        this.oWebControl.JS_CuttingPartWindow(
            1000 - iCoverRight,
            0,
            iCoverRight,
            600
        );
      }
      if (iCoverBottom != 0) {
        this.oWebControl.JS_CuttingPartWindow(
            0,
            600 - iCoverBottom,
            1000,
            iCoverBottom
        );
      }
    },/**
     * @codes:Array
     * */
    initPlugin(codes) {
      let that = this;
      // eslint-disable-next-line no-undef
      this.oWebControl = new WebControl({
        szPluginContainer: that.id, // 指定容器id
        iServicePortStart: 15900, // 指定起止端口号，建议使用该值
        iServicePortEnd: 15909,
        szClassId: "23BF3B0A-2C56-4D97-9C03-0CB103AA8F11", // 用于IE10使用ActiveX的clsid
        cbConnectSuccess: function () {
          that.oWebControl
              .JS_StartService("window", {
                // WebControl实例创建成功后需要启动服务
                dllPath: "./VideoPluginConnect.dll", // 值"./VideoPluginConnect.dll"写死
              })
              .then(
                  function () {
                    console.log("success");
                    that.oWebControl.JS_SetWindowControlCallback({
                      // 设置消息回调
                      cbIntegrationCallBack: that.cbIntegrationCallBack,
                    });
                    that.oWebControl
                        .JS_CreateWnd(that.id, that.swfWidth, that.swfHeight) //JS_CreateWnd创建视频播放窗口，宽高可设定
                        .then(function () {
                          console.log("成功", that);
                          that.init(codes); // 创建播放实例成功后初始化
                        });
                  },
                  function () {
                    // 启动插件服务失败
                    console.log("fail");
                  }
              );
        },
        cbConnectError: function () {
          // 创建WebControl实例失败
          console.log(that, "that");
          that.oWebControl = null;
          that.$message.error("插件未启动，正在尝试启动，请稍候...");
          // eslint-disable-next-line no-undef
          WebControl.JS_WakeUp("VideoWebPlugin://"); // 程序未启动时执行error函数，采用wakeup来启动程序
          that.initCount++;
          if (that.initCount < 3) {
            setTimeout(function () {
              that.initPlugin();
            }, 3000);
          } else {
            that.$message.error("插件启动失败，请检查插件是否安装！");
            that.dialogVisible = true;
          }
        },
        cbConnectClose: function (bNormalClose) {
          // 异常断开：bNormalClose = false
          // JS_Disconnect正常断开：bNormalClose = true
          console.log("cbConnectClose");
          that.oWebControl = null;
        },
      });
    },
    init(codes) {
      let that = this;
      this.getPubKey(function () {
        var appkey = "22222222";
        var secret = that.setEncrypt("xxxxxxxxxxx");
        var ip = "122.136.138.169";
        var playMode = 0; //这个是播放模式，0是预览，1是回放
        var port = 443;
        var snapDir = "D:\\SnapDir";
        var videoDir = "D:\\VideoDir";
        var layout = that.layout;
        var enableHTTPS = 1;
        var encryptedFields = "secret";
        var showToolbar = 1;
        var showSmart = 0;
        var buttonIDs = "0,16,256,257,258,259,260,512,513,514,515,516,517,768,769";
        that.oWebControl
            .JS_RequestInterface({
              funcName: "init",
              argument: JSON.stringify({
                appkey: appkey,
                secret: secret,
                ip: ip,
                playMode: playMode,
                port: port,
                snapDir: snapDir,
                videoDir: videoDir,
                layout: layout,
                enableHTTPS: enableHTTPS,
                encryptedFields: encryptedFields,
                showToolbar: showToolbar,
                showSmart: showSmart,
                buttonIDs: buttonIDs,
              }),
            })
            .then(function (oData) {
              that.oWebControl.JS_Resize(that.swfWidth, that.swfHeight);
              that.startClickFn(codes);
            });
      });
    },
    //公钥的获取
    getPubKey(callback) {
      let that = this;
      this.oWebControl
          .JS_RequestInterface({
            funcName: "getRSAPubKey",
            argument: JSON.stringify({
              keyLength: 1024,
            }),
          })
          .then(function (oData) {
            console.log(oData);
            if (oData.responseMsg.data) {
              that.pubKey = oData.responseMsg.data;
              callback();
            }
          });
    },
    setEncrypt(value) {
      // eslint-disable-next-line no-undef
      let encrypt = new JSEncrypt();
      encrypt.setPublicKey(this.pubKey);
      return encrypt.encrypt(value);
    },
    startClickFn(code) {
      var cameraIndexCode = code;
      var streamMode = 0;
      var transMode = 1;
      var gpuMode = 0;
      var wndId = -1;
      this.oWebControl.JS_RequestInterface({
        funcName: "startPreview",
        argument: JSON.stringify({
          cameraIndexCode: cameraIndexCode,
          streamMode: streamMode,
          transMode: transMode,
          gpuMode: gpuMode,
          wndId: wndId,
        }),
      });
    },
    stopClickFn() {
      if (this.oWebControl && this.oWebControl.JS_RequestInterface) {
        this.oWebControl.JS_RequestInterface({
          funcName: "stopAllPreview",
        });
      }
    },
    des() {
      if (this.oWebControl != null) {
        this.oWebControl.JS_HideWnd();
        this.oWebControl.JS_Disconnect().then(
            function () {
            },
            function () {
            }
        );
      }
    },
    //下载安装包
    handleDownloadExe() {
      this.dialogVisible = false;
      if (this.downloadUrl) {
        this.downloadFile(this.downloadUrl);
      } else {
        console.log("下载地址为空");
      }
      this.$emit("clickDownload");
    },
    //下载文件
    downloadFile(url, fileName = "") {
      const a = document.createElement("a");
      a.style.display = "none";
      if (fileName) {
        a.setAttribute("download", fileName);
      }
      a.setAttribute("href", url);
      document.body.appendChild(a);
      a.click();
      window.URL.revokeObjectURL(a.href);
      document.body.removeChild(a);
    }
  }
}
</script>
<style lang="scss" scoped>
.showvideo {
  width: 600px;
  height: 400px;
  /*border: 1px solid red;*/
  background-color: rgba(0, 0, 0, .3);
  border: 1px solid yellow;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}
</style>