<template>
    <div>
        <!--    头部-->
        <div style="display: flex; height: 60px; line-height: 60px; border-bottom: 1px solid #eee">
            <div style="width: 300px; display: flex; padding-left: 30px">
                <div style="width: 60px">
                    <img src="../../assets/registerbg.png" alt=""
                        style="width: 50px; position: relative; top: 5px; right: 0">
                </div>
                <div style="flex: 1">欢迎来到c语言学习平台</div>
            </div>
            <div style="flex: 1">
                <el-menu :default-active="'1'" class="el-menu-demo" mode="horizontal" router>
                    <el-menu-item index="/front/home">首页</el-menu-item>
                    <el-menu-item index="/front/cLanguage">C语言基础知识</el-menu-item>
                    <el-menu-item index="/front/video">C语言教学视频</el-menu-item>
                    <el-menu-item index="/front/article">C语言论坛交流</el-menu-item>
                    <el-submenu index="2" class="el-menu-demo1">
                        <template slot="title">我的工作台</template>
                        <el-menu-item><router-link to="/front/cOnlineEditor" target="_blank"
                                style="text-decoration: none">c语言在线编译器</router-link></el-menu-item>
                        <el-menu-item><router-link to="/front/cChallenge" target="_blank"
                                style="text-decoration: none">C语言编程题练习挑战</router-link></el-menu-item>
                        <el-menu-item><router-link to="/front/releaseArticle" target="_blank"
                                style="text-decoration: none">个人论坛文章管理</router-link></el-menu-item>
                        <el-menu-item><router-link to="/front/chat" target="_blank"
                                style="text-decoration: none">实时交流</router-link></el-menu-item>
                    </el-submenu>
                </el-menu>
            </div>
            <div style="width: 200px">
                <div v-if="!user.username" style="text-align: right; padding-right: 30px">
                    <el-button @click="$router.push('/login')">登录</el-button>
                    <el-button @click="$router.push('/register')">注册</el-button>
                </div>
                <div v-else>
                    <el-dropdown style="width: 150px; cursor: pointer; text-align: right">
                        <div style="display: inline-block">
                            <img :src="user.avatarUrl" alt="" 
                                style="width: 30px; border-radius: 50%; position: relative; top: 10px; right: 5px">
                            <span>{{ user.nickname }}</span><i class="el-icon-arrow-down" style="margin-left: 5px"></i>
                        </div>
                        <el-dropdown-menu slot="dropdown" style="width: 100px; text-align: center">
                            <el-dropdown-item style="font-size: 14px; padding: 5px 0">
                                <router-link to="/front/fPassword" style="text-decoration: none">修改密码</router-link>
                            </el-dropdown-item>
                            <el-dropdown-item style="font-size: 14px; padding: 5px 0">
                                <router-link to="/front/fPerson" style="text-decoration: none">个人信息</router-link>
                            </el-dropdown-item>
                            <el-dropdown-item style="font-size: 14px; padding: 5px 0" >
                                <router-link to="/" style="text-decoration: none">去后台</router-link>
                            </el-dropdown-item>
                            <el-dropdown-item style="font-size: 14px; padding: 5px 0">
                                <span style="text-decoration: none" @click="logout">退出</span>
                            </el-dropdown-item>
                        </el-dropdown-menu>
                    </el-dropdown>
                </div>
            </div>
        </div>

        <div style="width: 1000px; margin: 0 auto">
            <router-view @refreshUser="getUser"/>
        </div>
        <!-- 底部 -->
        <!-- <div
            style="height: 60px; line-height: 60px; text-align: center; background-color: #333; color: #fff; position: fixed; bottom: 0; left: 0; right: 0;">
            <div style="display: flex; justify-content: center; align-items: center;">
                <div style="margin-right: 20px;">
                    <i class="el-icon-star-on" style="font-size: 20px;"></i> 精彩内容
                </div>
                <div style="margin-right: 20px;">
                    <i class="el-icon-chat-line-square" style="font-size: 20px;"></i> 在线交流
                </div>
                <div>
                    <i class="el-icon-s-flag" style="font-size: 20px;"></i> 学习成就
                </div>
            </div>
        </div> -->


    </div>
</template>

<script>
export default {
    name: "Front",
    data() {
        return {
            user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {}
        }
    },
    created() {

    },
    methods: {
        logout() {
            this.$store.commit("logout")
            this.$message.success("退出成功")
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
.item {
    display: inline-block;
    width: 100px;
    text-align: center;
    cursor: pointer
}
/* 通过CSS覆盖Element UI的默认激活菜单项背景颜色 */
.el-menu-item.is-active {
  background-color: gray !important; /* 使用 !important 确保样式优先级 */
}
/* 改变鼠标悬停时菜单项的背景颜色 */
.el-menu-item:hover {
  background-color: gray !important; /* 无需使用 !important，除非有其他样式冲突 */
}
.item a {
    color: #ebeff3;
}
.el-menu-demo1:hover {
  background-color: #e84141  !important; /* 设置悬浮时的背景色 */
}
.item:hover {
    background-color: rgb(246, 214, 219);
}
</style>
