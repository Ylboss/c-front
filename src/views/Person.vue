<template>
    <el-card style="width: 500px;">
        <el-form label-width="80px" size="small">
            <el-upload class="avatar-uploader" :action="'http://localhost:9090/file/upload'" :show-file-list="false"
                accept="image/jpeg,image/png,image/gif" :on-success="handleAvatarSuccess"
                :data="{ uploaderName: user.username, purpose: '后台用户上传头像' }">
                <img v-if="form.avatarUrl" :src="form.avatarUrl" class="avatar">
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                <input type="hidden" name="uploaderName" value="user.username">
            </el-upload>
            <el-form-item label="用户名">
                <el-input v-model="form.username" disabled autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="昵称">
                <el-input v-model="form.nickname" autocomplete="off"></el-input>
            </el-form-item>

            <el-form-item label="角色">
                <!-- <el-input v-model="form.role" v-if="user.role !== 'ROLE_ADMIN'" disabled autocomplete="off"></el-input> -->
                <el-input v-model="form.role"  disabled autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="邮箱">
                <el-input v-model="form.email" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="电话">
                <el-input v-model="form.phone" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="地址">
                <el-input v-model="form.address" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="头像链接">
                <el-input type="textarea" v-model="form.avatarUrl" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="save">保 存</el-button>
            </el-form-item>
        </el-form>
    </el-card>
</template>

<script>
export default {
    name: "Person",
    data() {
        return {
            form: {},
            user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {}
        }
    },
    created() {
        this.getUser().then(res => {
            console.log(res)
            this.form = res
        })
    },
    methods: {
        async getUser() {
            return (await this.request.get("/user/username/" + this.user.username)).data
        },
        save() {
            if (this.user.role === 'USER_ADMIN' && this.form.role !== 'USER_ADMIN') {
                this.$message.error('不允许修改角色为 USER_ADMIN');
                return;
            }

            this.request.post("/user", this.form).then(res => {
                if (res) {
                    this.$message.success("保存成功");
                    // 触发父级更新User的方法
                    this.$emit("refreshUser");
                    // 更新浏览器存储的用户信息
                    this.getUser().then(res => {
                        res.token = JSON.parse(localStorage.getItem("user")).token;
                        localStorage.setItem("user", JSON.stringify(res));
                    });
                } else {
                    this.$message.error("保存失败");
                }
            });
        },
        handleAvatarSuccess(res) {
            this.form.avatarUrl = res
        }
    }
}
</script>

<style scoped>
.avatar-uploader {
    text-align: center;
    padding-bottom: 10px;
}

.avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
}

.avatar-uploader .el-upload:hover {
    border-color: #409EFF;
}

.avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 138px;
    height: 138px;
    line-height: 138px;
    text-align: center;
}

.avatar {
    width: 138px;
    height: 138px;
    display: block;
}
</style>
