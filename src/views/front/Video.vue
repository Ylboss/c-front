		
<template>
  <div class="video-container">
    <div class="search-container">
      <el-input v-model="keyword" placeholder="输入标题或上传者名称进行搜索" class="search-input"></el-input>
      <el-button class="search-button" type="primary" @click="search">搜索</el-button>
      <el-button type="warning" @click="reset">重置</el-button>
    </div>
    <el-card>
      <div v-for="item in filteredVideos" :key="item.id" class="video-item" @click="detail(item.id)">
        <span class="item-name">{{ item.name }}</span>
        <span class="item-size">文件大小：{{ item.size }} kb</span>
        <div>
          <span class="item-uploader">上传者: {{ item.uploaderName }}</span>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  name: "Video",
  data() {
    return {
      videos: [],
      keyword: "",
    }
  },
  async mounted() {
    try {
      // 发送请求获取数据
      const res = await this.request("/echarts/file/front/all");
      
      // 过滤出类型的视频数据
      this.videos = res.data.filter(v => v.type === 'mp4'&& v.courseId==null&& v.isDelete===false);
      console.log(this.videos)
    } catch (error) {
      // 如果请求出错，打印错误信息
      console.error(error);
    }
  },
  computed: {
    filteredVideos() {
      // 如果关键字为空
      if (this.keyword === "") {
        // 返回所有视频
        return this.videos;
      } else {
        // 返回包含关键字的视频
        return this.videos.filter(item => item.name.includes(this.keyword) || item.uploaderName.includes(this.keyword));
      }
    }
  },
  methods: {
    async detail(id) {
      try {
        // 跳转到视频详情页面，并携带视频ID作为查询参数
        await this.$router.push({ path: "/front/videoDetail", query: { id: id } });
      } catch (error) {
        // 捕获并打印可能出现的错误
        console.error(error);
      }
    },
    search() {
      this.videos = this.videos.filter(item => item.name.includes(this.keyword) || item.uploaderName.includes(this.keyword));
    },
    reset() {
      this.keyword = "";
      this.videos = this.videos.filter(v => v.type === 'mp4'); // 重置为所有视频信息
    }
  }
}
</script>

<style scoped>
.video-container {
  padding: 10px;
}

.video-item {
  margin: 10px 0;
  padding: 10px 0;
  color: #666;
  border-bottom: 1px dashed #ccc;
  font-size: 14px;
  cursor: pointer;
}

.item-name:hover {
  color: #3a8ee6;
  background: none;
}

.item-size {
  float: right;
  font-size: 12px;
  margin-top: 10px;
}

.item-uploader {
  float: right;
  font-size: 12px;
  margin-top: 10px;
}

.search-container {
  margin-top: 10px;
}

.search-container {
  display: flex;
  align-items: center;
}

.search-input {
  width: 300px;
  margin-right: 10px;
  border-radius: 20px;
}

.search-button {
  border-radius: 20px;
  background-color: #409EFF;
  color: #fff;
}
</style>