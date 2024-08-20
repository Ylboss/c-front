<!--
 * @Author: XUNYUYU
 * @Date: 2024-03-31 13:27:54
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-05-15 14:24:33
 * @FilePath: \graduation_design\src\views\Manage.vue
-->
<template>
    <div class="container">
      <div class="aside-wrapper">
        <el-aside class="aside" :width="sideWidth + 'px'" style="box-shadow: 2px 0 6px rgb(0 21 41 / 35%);">
          <Aside :isCollapse="isCollapse" :logoTextShow="logoTextShow"/>
        </el-aside>
      </div>
      <div class="main-wrapper">
        <el-container class="main-container">
          <el-header style="border-bottom: 1px solid #ccc;">
            <Header :collapseBtnClass="collapseBtnClass" @asideCollapse="collapse" :user="user"/>
          </el-header>
          <el-main>
            <router-view @refreshUser="getUser"/>
          </el-main>
        </el-container>
      </div>
    </div>
  </template>
<script>

    import Aside from "@/components/Aside";
    import Header from "@/components/Header";

    export default {
        name: 'Home',
        data() {
            return {
                collapseBtnClass: 'el-icon-s-fold',
                isCollapse: false,
                sideWidth: 200,
                logoTextShow: true,
                user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {}
            }
        },
        components: {
            Aside,
            Header
        },
        create() {
            // 从后台获取最新的User数据
            this.getUser()
        },
        methods: {
            collapse() {  // 点击收缩按钮触发
                this.isCollapse = !this.isCollapse
                if (this.isCollapse) {  // 收缩
                    this.sideWidth = 64
                    this.collapseBtnClass = 'el-icon-s-unfold'
                    this.logoTextShow = false
                } else {   // 展开
                    this.sideWidth = 200
                    this.collapseBtnClass = 'el-icon-s-fold'
                    this.logoTextShow = true
                }
            },
            getUser() {
                let username = localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")).username : ""
                if (username) {
                    // 从后台获取User数据
                    this.request.get("/user/username/" + username).then(res => {
                        // 重新赋值后台的最新User数据
                        this.user = res.data
                    })
                }
            }
        }
    }
</script>
<style>
  .container {
    display: flex;
    flex-direction: row;
  }
  .aside-wrapper {
    position: fixed;
    height: 100%;
  }
  .main-wrapper {
    margin-left: 200px;
    flex: 1;
  }
  .main-container {
    height: 100%;
  }
  .aside {
    height: 100%;
  }
</style>