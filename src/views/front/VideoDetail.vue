<template>
  <div>
    <div class='demo' id="dplayer-container"></div>
  </div>
</template>
<script>
import DPlayer from 'dplayer';

export default {
  name: "VideoDetail",
  data() {
    return {
      dplayerInstance: null, // 用于存储DPlayer实例的变量
      playerOptions: {
        
      },
    };
  },
  created() {
    let id = this.$route.query.id;
    this.request("/file/detail/" + id).then(res => {   
      this.playerOptions.video = { url: res.data.url, type: 'auto' }; // 设置视频源
      if (this.dplayerInstance) {
        this.dplayerInstance.destroy(); // 如果已经存在实例，则先销毁
      }
      this.initializeDPlayer(); // 初始化DPlayer
    });
  },
  mounted() {
    // 可以在这里进行DPlayer的初始配置，但实际的初始化在created钩子中完成，以确保视频源已加载
  },
  methods: {
    initializeDPlayer() {
      this.dplayerInstance = new DPlayer({
        container: document.getElementById('dplayer-container'), // 容器元素
        ...this.playerOptions, // 播放器的其他配置选项
      });
    },
    // ... (可能还有其他方法)
  },
  beforeDestroy() {
    if (this.dplayerInstance) {
      this.dplayerInstance.destroy(); // 组件销毁前清除DPlayer实例
    }
  }
};
</script>