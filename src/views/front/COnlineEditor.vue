<!--
 * @Author: XUNYUYU
 * @Date: 2024-04-03 22:25:34
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-05-07 17:48:43
 * @FilePath: \graduation_design\src\views\front\COnlineEditor.vue
-->
<template>
  <div class="wrapper">
    <el-container>
      <el-header>
        <h1>C语言编译器</h1>
      </el-header>
      <el-main>
        <el-input type="textarea" v-model="code" placeholder="请输入C语言代码" :rows="10"></el-input>
        <el-button type="primary" @click="compileCode">编译</el-button>
        <el-alert v-if="errorMessage" type="error" :description="errorMessage" show-icon></el-alert>
        <el-alert v-if="output" type="success" :description="output" show-icon></el-alert>
      </el-main>
    </el-container>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      code: '',
      errorMessage: '',
      output: ''
    };
  },
  methods: {
    compileCode() {
      axios.post('http://localhost:9090/api/compile', { code: this.code })
        .then(res => {
          // alert("结果是:"+res.data)

          if (res.status == "200") {
            this.output = res.data;
            console.log(this.output)
          } else {
            this.errorMessage = res.data.message;
          }
        })
        .catch(error => {
          console.error(error);
          this.errorMessage = '编译出错，请检查你的代码或稍后重试。';
          setTimeout(() => {
            this.errorMessage = '';
          }, 3000);
        });
    }
  }
};  
</script>

<style scoped>
.wrapper {
  height: 100vh;
  background-color: rgb(247, 227, 227);
  background-size: cover;
  background-position: center;
  overflow: hidden;
}
</style>