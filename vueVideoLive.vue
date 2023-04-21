<template>
  <div>
    <vue-video ref="vueVideo" :deviceSerial=deviceSerial :url=url />
  </div>
</template>

<script>
import qs from 'qs';
import VueVideo from '@/views/community/device/vueVideo';

export default {
  name: "VueVideoLive",
  components: {
    VueVideo,
  },
  props: {
  },
  data() {
    return {
      player: null,
      deviceSerial: '',
      url: ''
    }
  },
  watch: {
  },
  created() {
    const location = window.location;
    const params = qs.parse(location.hash.split('?')[1]);
    this.url = params.url;
    this.deviceSerial = params.deviceSerial;
  },
  mounted() {
  },
  methods: {
  }
};
</script>
