<template>
    <div>
      <el-card style="width: 500px;">
        <el-form label-width="80px" size="small">
          <el-upload
            class="avatar-uploader"
            :action="uploadUrl"
            :show-file-list="false"
            accept="image/jpeg,image/png,image/gif"
            :on-success="handleAvatarSuccess"
            :data="uploadData"
          >
            <img v-if="form.avatarUrl" :src="form.avatarUrl" class="avatar" />
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>
          <el-form-item label="用户名">
            <el-input v-model="form.username" disabled autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="昵称">
            <el-input v-model="form.nickname" autocomplete="off"></el-input>
          </el-form-item>
          <el-form-item label="角色">
                <el-input v-model="form.role" disabled autocomplete="off"></el-input>
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
          <el-form-item style="margin: 10px 0; text-align: right">
            <el-button type="text" size="medium" autocomplete="off" @click="handlePass">申请讲师权限</el-button>
          </el-form-item>
  
          <!-- 弹窗包含验证表单 -->
          <el-dialog title="申请讲师权限验证" :visible.sync="dialogVisible">
            <el-form label-width="100px" size="small">
              <el-form-item label="邮箱">
                <el-input size="medium" v-model="apply.email" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="验证码">
                <el-input size="medium" style="width: 100px" v-model="apply.code"></el-input>
                <el-button type="primary" size="medium" class="ml-5" @click="sendEmailCode">获取验证码</el-button>
              </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="passwordBack">确 定</el-button>
            </span>
          </el-dialog>
        </el-form>
      </el-card>
    </div>
  </template>
  
  <script>
  const UPLOAD_URL = 'http://localhost:9090/file/upload';
  const UPLOAD_PURPOSE = '前台用户上传头像';
  const EMAIL_TYPE = 3;
  
  export default {
    name: 'FPerson',
    data() {
      return {
        dialogVisible: false,
        apply: {
          email: '',
          code: '',
        },
        form: {},
        user: JSON.parse(localStorage.getItem('user') || '{}'),
      };
    },
    created() {
      this.getUserInfo();
    },
    computed: {
      uploadUrl() {
        return UPLOAD_URL;
      },
      uploadData() {
        return {
          uploaderName: this.user.username,
          purpose: UPLOAD_PURPOSE,
        };
      },
    },
    methods: {
      passwordBack() {
        this.request.put('/user/apply', this.apply).then((res) => {
          if (res.code === '200') {
            this.$message.success('已经成功修改了角色权限');
            this.dialogVisible = false;
            this.getUserInfo();
          } else if (res.code === '500') {
            this.$message.error('该邮箱没有对应账号');
          } else {
            this.$message.error(res.msg);
          }
        });
      },
      sendEmailCode() {
        const email = this.apply.email;
        // 发送邮箱验证码
        this.request.get(`/user/email/${email}/${EMAIL_TYPE}`).then((res) => {
          if (res.code === '200') {
            this.$message.success('发送成功');
          } else {
            this.$message.error(res.msg);
          }
        });
      },
      handlePass() {
        this.dialogVisible = true;
        this.apply = {
          email: this.form.email,
          code: '',
        };
      },
      async getUserInfo() {
        const res = await this.request.get(`/user/username/${this.user.username}`);
        this.form = res.data;
      },
      save() {
        this.request.post('/user', this.form).then((res) => {
          if (res) {
            this.$message.success('保存成功');
            // 触发父级更新User的方法
            this.$emit('refreshUser');
            // 更新浏览器存储的用户信息
            this.getUserInfo().then(() => {
              const user = JSON.parse(localStorage.getItem('user') || '{}');
              user.token = this.form.token;
              localStorage.setItem('user', JSON.stringify(user));
            });
          } else {
            this.$message.error('保存失败');
          }
        });
      },
      handleAvatarSuccess(res) {
        this.form.avatarUrl = res;
      },
    },
  };
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
    border-color: #409eff;
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