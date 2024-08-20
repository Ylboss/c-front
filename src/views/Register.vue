<!--
 * @Author: XUNYUYU
 * @Date: 2024-04-16 14:05:57
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-05-12 17:53:40
 * @FilePath: \graduation_design\src\views\Register.vue
-->
<template>
    <div class="wrapper">
        <div class="register-box"
            style="margin: 100px auto; background-color: #fff; width: 350px; height: 480px; padding: 20px; border-radius: 10px">
            <div style="margin:20px 0;text-align: center; font-size: 24px"><b>注册</b></div>
            <el-form :model="user" ref="userForm" :rules="rules">
                <el-form-item prop="username">
                    <el-input size="medium" style="margin: 5px 0" prefix-icon="el-icon-user" v-model="user.username"
                        placeholder="请输入账号"></el-input>
                </el-form-item>
                <el-form-item prop="email">
                    <el-input size="medium" style="margin: 5px 0" prefix-icon="el-icon-email" placeholder="请输入邮箱"
                         v-model="user.email"></el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <el-input size="medium" style="margin: 5px 0" prefix-icon="el-icon-lock" placeholder="请输入密码"
                        show-password v-model="user.password"></el-input>
                </el-form-item>
                
                <el-form-item prop="confirmPassword">
                    <el-input size="medium" style="margin: 5px 0" prefix-icon="el-icon-lock" placeholder="请确认密码"
                        show-password v-model="user.confirmPassword"></el-input>
                </el-form-item>
                
                <el-form-item prop="role">
                    <el-select size="medium" style="margin: 5px 0" placeholder="请选择角色" v-model="user.role">
                        <el-option label="普通用户" value="ROLE_USER"></el-option>
                    </el-select>
                </el-form-item>

                <el-form-item style="margin:10px 0;text-align: right">
                    <el-button type="primary" size="small" autocomplete="off" @click="register()">注册</el-button>
                    <el-button type="warning" size="small" autocomplete="off"
                        @click="$router.push('/login')">返回登录</el-button>
                </el-form-item>
            </el-form>
        </div>
    </div>
</template>
<script>
export default {
    name: "Login",
    data() {
        return {
            user: {},
            rules: {
                username: [
                    { required: true, message: '请输入用户名', trigger: 'blur' },
                    { min: 3, max: 10, message: '长度在 3 到 5 个字符', trigger: 'blur' }
                ],
                email: [    
                    { required: true, message: '请输入邮箱', trigger: 'blur' },
                    { type: 'email', message: '邮箱格式不正确', trigger: 'blur' } // 添加邮箱格式验证规则
                ],
                password: [
                    { required: true, message: '请输入密码', trigger: 'blur' },
                    { min: 1, max: 20, message: '长度在 1 到 20 个字符', trigger: 'blur' }
                ],
                confirmPassword: [
                    { required: true, message: '请输入确认密码', trigger: 'blur' },
                    { min: 1, max: 20, message: '长度在 1 到 20 个字符', trigger: 'blur' }
                ],
            }

        }
    },
    methods: {
        register() {
            this.$refs.userForm.validate((valid) => {
                if (valid) {
                    if (this.user.password !== this.user.confirmPassword) {
                        this.$message.error('两次密码输入不一致')
                        return false;
                    }

                    this.request.post('/user/register', this.user).then(res => {
                        if (res.code == '200') {
                            this.$message.success('注册成功')
                             // 注册成功后跳转到登录页面
                             this.$router.push('/login');
                        }
                        else {
                            this.$message.error(res.msg)
                        }
                    })

                }
            })
        }
    }
}
</script>
<style scoped>
.wrapper {
    height: 100vh;
    background-image: url("C:\Users\yl\Desktop\bs\graduation_design\src\assets\registerbg.png");
    overflow: hidden;

}
</style>