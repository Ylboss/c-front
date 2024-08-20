<!--
 * @Author: XUNYUYU
 * @Date: 2024-04-12 01:11:58
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-05-15 17:29:18
 * @FilePath: \graduation_design\src\views\Login.vue
-->
<template>
    <div class="wrapper">
        <div style="margin: 100px auto; background: white; width: 500px; border-radius: 10px; overflow: hidden">
            <el-tabs type="card" v-model="activeName">

                <el-tab-pane label="账号密码登录" name="first">
                    <div
                        style="margin: 20px auto; background-color: #fff; width: 350px; height: 220px; padding: 20px; border-radius: 10px">
                        <el-form :model="user" :rules="rules" ref="userForm">
                            <el-form-item prop="username">
                                <el-input size="medium" prefix-icon="el-icon-user" v-model="user.username"></el-input>
                            </el-form-item>
                            <el-form-item prop="password">
                                <el-input size="medium" prefix-icon="el-icon-lock" show-password
                                    v-model="user.password"></el-input>
                            </el-form-item>

                            <el-form-item style="margin: 10px 0;">
                                <el-checkbox v-model="user.agree">我已阅读并同意</el-checkbox>
                                <el-button type="text" @click="showResponsibilityBook">相关责任书</el-button>
                            </el-form-item>
                            <el-form-item style="margin: 10px 0; text-align: right">
                                <el-button type="warning" size="medium" autocomplete="off"
                                    @click="$router.push('/register')">
                                    前往注册
                                </el-button>
                                <el-button type="primary" size="medium" autocomplete="off" @click="loginAccount"
                                    :disabled="!user.agree">
                                    登录
                                </el-button>
                            </el-form-item>
                            <el-form-item style="margin: 10px 0; text-align: right">
                                <el-button type="text" size="medium" autocomplete="off"
                                    @click="handlePass">找回密码</el-button>
                            </el-form-item>
                            
                        </el-form>
                    </div>
                </el-tab-pane>

                <el-tab-pane label="邮箱登录" name="second">
                    <div
                        style="margin: 20px auto; background-color: #fff; width: 350px; height: 220px; padding: 20px; border-radius: 10px">
                        <el-form :model="user" :rules="rules" ref="userForm">
                            <el-form-item prop="email">
                                <el-input size="medium" prefix-icon="el-icon-message" v-model="user.email"></el-input>
                            </el-form-item>
                            <el-form-item prop="code">
                                <el-input size="medium" prefix-icon="el-icon-lock" style="width: 190px"
                                    v-model="user.code"></el-input>
                                <el-button type="primary" size="medium" class="ml-5"
                                    @click="sendEmailCode(1)">获取验证码</el-button>
                            </el-form-item>

                            <el-form-item style="margin: 10px 0; text-align: right">
                                <el-button type="warning" size="medium" autocomplete="off"
                                    @click="$router.push('/register')">前往注册</el-button>
                                <el-button type="primary" size="medium" autocomplete="off"
                                    @click="loginEmail">登录</el-button>

                            </el-form-item>
                            <el-form-item style="margin: 10px 0; text-align: right">
                                <el-button type="text" size="mid" autocomplete="off"
                                    @click="handlePass">找回密码</el-button>
                            </el-form-item>
                        </el-form>
                    </div>
                </el-tab-pane>

            </el-tabs>
        </div>

        <el-dialog title="找回密码" :visible.sync="dialogFormVisible" width="40%" class="password-dialog">
            <el-form label-width="100px" size="small">
                <el-form-item label="邮箱">
                    <el-input size="medium" v-model="pass.email" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="验证码">
                    <el-input size="medium" style="width: 100px" v-model="pass.code"></el-input>
                    <el-button type="primary" size="medium" class="ml-5" @click="sendEmailCode(2)">获取验证码</el-button>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="passwordBack">重置密码</el-button>
            </div>
        </el-dialog>

        <el-dialog title="相关责任书" :visible.sync="responsibilityBookVisible" width="50%">
            <div>
                <h3>责任书</h3>
                <p>尊敬的用户：</p>
                <p>欢迎使用我们的服务。在使用本服务之前，请您务必仔细阅读并透彻理解本责任书的全部内容。如果您不同意本责任书的任何内容，或者无法准确理解相关条款的含义，请不要进行后续操作。</p>
                <p>1. 本服务仅供个人学习和研究使用，禁止用于商业用途。</p>
                <p>2. 在使用本服务过程中，您应当遵守国家相关法律法规，不得利用本服务进行任何违法、违规或侵权行为。</p>
                <p>3. 您应当妥善保管您的账号和密码，不得将其提供给他人使用，否则由此产生的一切后果由您自行承担。</p>
                <p>4. 本服务可能会收集和使用您的个人信息，但我们将严格遵守相关法律法规，保护您的个人信息安全。</p>
                <p>5. 本服务可能会包含第三方提供的内容或链接，但我们对这些内容的准确性和合法性不承担任何责任。</p>
                <p>6. 您在使用本服务过程中产生的任何纠纷或损失，我们将不承担任何责任。</p>
                <p>请您在使用本服务之前，认真阅读并理解本责任书的全部内容。如果您对任何条款有疑问，可以随时联系我们进行咨询。一旦您开始使用本服务，即表示您已经完全理解并同意遵守本责任书的全部内容。</p>
            </div>
            <span slot="footer" class="dialog-footer">
                <el-button type="primary" @click="responsibilityBookVisible = false">确定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import { resetRouter, setRoutes } from "@/router";


export default {
    name: "Login",
    data() {
        return {
            activeName: 'first',
            user: {
                agree: false,
            },
            responsibilityBookVisible: false, // 责任书弹窗的显示状态
            pass: {},
            code: '',
            dialogFormVisible: false,
            // 图片验证码
            identifyCode: '',
            // 验证码规则
            identifyCodes: '3456789ABCDEFGHGKMNPQRSTUVWXY',
            rules: {
                username: [
                    { required: true, message: '请输入用户名', trigger: 'blur' },
                    { min: 1, max: 20, message: '长度在 20个字符以内', trigger: 'blur' }
                ],
                password: [
                    { required: true, message: '请输入密码', trigger: 'blur' },
                    { min: 1, max: 20, message: '长度在 20个字符以内', trigger: 'blur' }
                ]
            }
        }
    },

    mounted() {
        // 重置路由
        resetRouter()
        this.refreshCode()
    },
    methods: {
        showResponsibilityBook() {
            this.responsibilityBookVisible = true
        },
        sendEmailCode(type) {
            let email;
            if (type === 1) {
                email = this.user.email
            } else if (type === 2) {
                email = this.pass.email
            }
            if (!email) {
                this.$message.warning("请输入邮箱账号")
                return
            }
            if (!/^\w+((.\w+)|(-\w+))@[A-Za-z0-9]+((.|-)[A-Za-z0-9]+).[A-Za-z0-9]+$/.test(email)) {
                this.$message.warning("请输入正确的邮箱账号")
                return
            }
            // 发送邮箱验证码
            this.request.get("/user/email/" + email + "/" + type).then(res => {
                if (res.code === '200') {
                    this.$message.success("发送成功")
                } else {
                    this.$message.error(res.msg)
                }
            })
        },
        loginAccount() {
            if (!this.user.agree) {
                this.$message.warning("请先同意相关责任书");
                return;
            }
            // if (this.code !== this.identifyCode.toLowerCase()) {
            //   this.$message({
            //     type: "error",
            //     message: "验证码错误"
            //   })
            //   return;
            // }
            this.$refs['userForm'].validate((valid) => {
                if (valid) {  // 表单校验合法
                    this.request.post("/user/login", this.user).then(res => {
                        if (res.code == '200') {
                            localStorage.setItem("user", JSON.stringify(res.data)) // 存储用户信息到浏览器
                            localStorage.setItem("menus", JSON.stringify(res.data.menus))
                            // 动态设置当前用户的路由
                            setRoutes()
                            this.$message.success("登录成功")
                            
                            if (res.data && res.data.role) {
                                switch (res.data.role) {
                                    case 'ROLE_USER':
                                        this.$router.push("/front");
                                        break;
                                    default:
                                        this.$router.push("/");
                                        break;
                                }
                            } else {
                                // 处理 res.data 不存在或 role 不存在的情况
                                this.$router.push("/404");
                            }

                        } else {
                            this.$message.error(res.msg)
                        }
                    })
                }
            });
        },
        
        loginEmail() {
            if (!this.user.email) {
                this.$message.warning("请输入邮箱")
                return
            }
            if (!this.user.code) {
                this.$message.warning("请输入验证码")
                return
            }
            this.request.post("/user/loginEmail", this.user).then(res => {
                if (res.code === '200') {
                    localStorage.setItem("user", JSON.stringify(res.data))  // 存储用户信息到浏览器
                    localStorage.setItem("menus", JSON.stringify(res.data.menus))  // 存储用户信息到浏览器

                    // 动态设置当前用户的路由
                    setRoutes()
                    
                    
                    if (res.data && res.data.role) {
                        switch (res.data.role) {
                            case 'ROLE_USER':
                                this.$router.push("/front");
                                break;
                            case 'ROLE_ADMIN':
                                this.$router.push("/");
                                break;
                            default:
                                this.$router.push("/404");
                                break;
                        }
                    } else {
                        // 处理 res.data 不存在或 role 不存在的情况
                        this.$router.push("/404");
                    }
                } else {
                    this.$message.error(res.msg)
                }
            })
        },
        // 切换验证码
        refreshCode() {
            this.identifyCode = ''
            this.makeCode(this.identifyCodes, 4)
        },
        // 生成随机验证码
        makeCode(o, l) {
            for (let i = 0; i < l; i++) {
                this.identifyCode += this.identifyCodes[Math.floor(Math.random() * (this.identifyCodes.length))]
            }
        },
        handlePass() {
            this.dialogFormVisible = true
            this.pass = {}
        },
        passwordBack() {
            this.request.put("/user/reset", this.pass).then(res => {
                if (res.code === '200') {
                    this.$message.success("重置密码成功，新密码为：123，请尽快修改密码");
                    this.dialogFormVisible = false;
                } else if (res.code === '500') {
                    this.$message.error("该邮箱没有对应账号");
                } else {
                    this.$message.error(res.msg);
                }
            });
        }
    }
}
</script>

<style>
.wrapper {
    height: 100vh;
    background-image: url("../assets/QQ图片20220927154339.jpg");
    background-size: cover;
    background-position: center;
    overflow: hidden;
}

.password-dialog {
    background-color: #f5f5f5;
    border-radius: 5px;
    padding: 20px;
}

.password-dialog .el-dialog__header {
    background-color: #409eff;
    color: white;
    font-size: 18px;
    font-weight: bold;
    padding: 10px;
}

.password-dialog .el-dialog__body {
    padding: 20px;
}

.password-dialog .el-form-item__label {
    font-size: 14px;
    font-weight: bold;
    margin-bottom: 10px;
}

.password-dialog .el-input__inner {
    border-radius: 3px;
    height: 36px;
    line-height: 36px;
    padding: 0 10px;
}

.password-dialog .el-button {
    background-color: #409eff;
    border-radius: 3px;
    color: white;
    font-size: 14px;
    font-weight: bold;
    height: 36px;
    line-height: 36px;
    padding: 0 10px;
}

.password-dialog .el-button--primary {
    background-color: #1890ff;
}

.password-dialog .el-button--text {
    color: #409eff;
}

.password-dialog .el-button--text:hover {
    color: #1890ff;
}

.password-dialog .dialog-footer {
    padding: 20px;
    text-align: right;
}
</style>