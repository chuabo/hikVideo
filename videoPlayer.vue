<template>
  <div>
    <el-dialog
      :title="videoDialogTitle"
      top="10vh"
      :visible.sync="videoVisible"
      :modal="false"
      width="650px" :before-close="videoClose">
      <div>
        <Video ref="video" :accessToken=accessToken :deviceSerial=deviceSerial :template="template" />
      </div>
      <span slot="footer" class="dialog-footer">
        <el-row :gutter="20">
          <el-col :span="14">
            <el-button type="primary" @click="videoClose()">关闭</el-button>
          </el-col>
        </el-row>
      </span>
    </el-dialog>

    <el-dialog
      :title="videoDialogTitle"
      top="10vh"
      :visible.sync="vueVideoVisible"
      :modal="false"
      width="650px" :before-close="vueVideoClose">
      <div>
        <vue-video ref="vueVideo" :deviceSerial="deviceSerial" :url="url" />
      </div>
      <span slot="footer" class="dialog-footer">
        <el-row :gutter="20">
          <el-col :span="14">
            <el-button type="primary" @click="vueVideoClose()">关闭</el-button>
          </el-col>
        </el-row>
      </span>
    </el-dialog>

    <el-dialog
      :title="videoDialogTitle"
      top="10vh"
      :visible.sync="hikVideoVisible"
      :modal="false"
      width="650px" :before-close="vueVideoClose">
      <div>
        <hik-video ref="hikVideo" style="width: 600px;height: 400px;" :cameraIndexCode="cameraIndexCode" :url="url" />
      </div>
      <span slot="footer" class="dialog-footer">
        <el-row :gutter="20">
          <el-col :span="14">
            <el-button type="primary" @click="hikVideoClose()">关闭</el-button>
          </el-col>
        </el-row>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import Video from '@/views/community/device/video';
import VueVideo from '@/views/community/device/vueVideo';
import HikVideo from '@/views/community/device/hikVideo';

export default {
  name: "VideoPlayer",
  components: {
    Video,
    VueVideo,
    HikVideo,
  },
  props: {
    template: {
      type: String,
      default: 'simple'
    },
    videoDialogTitle: {
      type: String,
      default: ''
    },
    accessToken: {
      type: String,
      default: ''
    },
    deviceSerial: {
      type: String,
      default: ''
    },
    url: {
      type: String,
      default: ''
    },
    cameraIndexCode: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      videoVisible: false,
      vueVideoVisible: false,
      hikVideoVisible: false
    }
  },
  watch: {
    accessToken: {
      handler(val) {
        if (val) {
          this.videoVisible = true;
        }
      },
      immediate: true
    },
    url: {
      handler(val) {
        if (val) {
          this.vueVideoVisible = true;
        }
      },
      immediate: true
    },
    cameraIndexCode: {
      handler(val) {
        if (val) {
          this.hikVideoVisible = true;
        }
      },
      immediate: true
    }
  },
  mounted() {
  },
  methods: {
    videoClose() {
      this.videoVisible = false
      this.$refs.video.stop();
      this.$emit('videoClose');
    },
    vueVideoClose() {
      this.vueVideoVisible = false
      this.$refs.vueVideo.stop();
      this.$emit('videoClose');
    },
    hikVideoClose() {
      this.hikVideoVisible = false
      this.$refs.hikVideo.close();
      this.$emit('videoClose');
    }
  }
};
</script>
