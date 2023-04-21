��index.html��������hik�е������ļ�

VideoPlayerʹ�÷���

<template>
  <div>
    <VideoPlayer ref="videoPlayer" :videoDialogTitle="videoDialogTitle" :accessToken="accessToken" :deviceSerial="deviceSerial" :cameraIndexCode="cameraIndexCode" :template="template" :url="url" @videoClose="videoClose()" />
  </div>
</template>

import VideoPlayer from './videoPlayer';

export default {
  components: {
    VideoPlayer
  },
  methods: {
    showVideo(item) {
      this.videoDialogTitle = '��Ƶ�鿴[' + item.deviceName + ']';
      if (item.playParams != '' && item.playParams != null) {
        const params = item.playParams.split('/');
        getAccessToken(params[0], params[1], item.deviceSerial).then(res => {
          this.accessToken = res.data;
          this.deviceSerial = item.deviceSerial;
        });
      } else {
        // ʹ�ú����������
        this.cameraIndexCode = item.deviceSerial;
        // ʹ��m3u8��ʽ����
        /*getPreviewUrl(item.deviceSerial).then(res => {
          const data = JSON.parse(res.data.data);
          if (data instanceof Object) {
            this.url = data.data.url;
          }
        })*/
      }
    },
    videoClose() {
      this.deviceSerial = null;
      this.accessToken = null;
      this.cameraIndexCode = null;
      this.url = null;
    }
}