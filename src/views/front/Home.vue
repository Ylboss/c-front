<template>
    <div>
      <!-- 轮播图 -->
      <div style="margin: 10px 0">
        <el-carousel height="450px" :interval="10000">
          <el-carousel-item v-for="item in imgs" :key="item">
            <img :src="item" alt="" style="width: 100%" />
          </el-carousel-item>
        </el-carousel>
      </div>
      <!-- 搜索框 -->
      <!-- <div class="search-container">
        <el-input v-model.trim="searchParams.keyword" placeholder="输入标题或上传者名称进行搜索" class="search-input"
          prefix-icon="el-icon-search" clearable @clear="resetSearchParams" @keyup.enter="searchFiles"></el-input>
        <el-button class="search-button" type="primary" @click="searchFiles">
          搜索
        </el-button>
        <el-button type="warning" @click="resetSearchParams">重置</el-button>
      </div> -->
      <!-- 文件列表 -->
      <div style="margin: 10px 0">
        <el-row :gutter="10">
          <el-col :span="8" v-for="item in files" :key="item.id" style="margin-bottom: 10px">
            <div class="file-container">
              <img :src="item.url" alt="" style="width: 100%" />
              <div class="file-info">{{ item.name }}</div>
              <div class="file-info">作者:{{ item.uploaderName }}</div>
              <div class="file-action">
                <el-button type="primary" @click="showPaymentDialog(item)">购买</el-button>
              </div>
            </div>
          </el-col>
        </el-row>
      </div>
      <!-- 付款窗口 -->
      <el-dialog title="付款窗口" :visible.sync="paymentDialogVisible" width="30%">
        <p>请输入付款金额：</p>
        <el-input v-model.number="paymentAmount" placeholder="请输入付款金额"></el-input>
        <el-button type="primary" @click="confirmPayment">确认付款</el-button>
      </el-dialog>
    </div>
  </template>
  
  <script>
  const FILE_URL = "/echarts/file/front/all";
  
  export default {
    name: "FrontHome",
    data() {
      return {
        paymentDialogVisible: false,
        paymentAmount: 0,
        currentItem: null,
        imgs: [],
        files: [],
        searchParams: {
          keyword: "",
        },
      };
    },
    created() {
      this.fetchFiles();
      this.imgs = [
        "http://localhost:9090/file/b8d54b7bffd64b27936c87328c254375.jpg",
        "http://localhost:9090/file/140da7007ac0478c9dd20469e6f620c8.jpg",
        "http://localhost:9090/file/c8cedbba163d46ff8a5003d2b56836e0.jpg",
      ];
    },
    methods: {
      // 获取文件列表
      fetchFiles() {
        this.request
          .get(FILE_URL, { params: this.searchParams })
          .then((res) => {
            // 过滤掉不支持的文件类型
            const files = res.data.filter((v) => ["doc", "pdf", "webp"].includes(v.type));
            // 根据搜索关键字过滤文件
            if (this.searchParams.keyword) {
              this.files = files.filter((v) => v.name.includes(this.searchParams.keyword) || v.uploaderName.includes(this.searchParams.keyword));
            } else {
              this.files = files;
            }
          })
          .catch((error) => {
            console.error("Error fetching files:", error);
            this.$message.error("获取文件列表失败！");
          });
      },
      // 显示付款窗口
      showPaymentDialog(item) {
        this.paymentDialogVisible = true;
        this.currentItem = item;
      },
      // 确认付款
      confirmPayment() {
        if (this.paymentAmount <= 0) {
          this.$message.error("付款金额必须大于0！");
          return;
        }
        this.paymentDialogVisible = false;
        this.$message.success("支付成功！");
      },
      // 搜索文件
      searchFiles() {
        this.fetchFiles();
      },
      // 重置搜索条件
      resetSearchParams() {
        this.searchParams.keyword = "";
        this.fetchFiles();
      },
    },
  };
  </script>
  
  <style>
  .file-container {
    border: 1px solid #ccc;
    padding-bottom: 10px;
  }
  
  .file-info {
    color: #666;
    padding: 10px;
  }
  
  .file-action {
    padding: 10px;
  }
  
  .search-container {
    margin-top: 10px;
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
    background-color: #409eff;
    color: #fff;
  }
  </style>