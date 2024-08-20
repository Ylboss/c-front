<template>
    <div class="article-detail">
        <!-- 文章头部信息 -->
        <div class="article-header">
            <div class="article-title">{{ article.name }}</div>
            <div class="article-info">
                <i class="el-icon-user-solid"></i> <span>{{ article.user }}</span>
                <i class="el-icon-time"></i> <span>{{ article.time }}</span>
            </div>
        </div>

        <!-- 文章内容展示 -->
        <div class="article-content">
            <mavon-editor class="md" :value="article.content" :subfield="false" :defaultOpen="'preview'"
                :toolbarsFlag="false" :editable="false" :scrollStyle="true" :ishljs="true" />
        </div>

        <!-- 评论区域 -->
        <div class="comment-section">
            <!-- 评论输入框 -->
            <div class="comment-form">
                <el-input size="small" type="textarea" v-model="commentForm.content"></el-input>
                <el-button type="primary" size="small" @click="save">评论</el-button>
            </div>

            <!-- 评论列表 -->
            <div class="comment-list">
                <div v-for="item in comments" :key="item.id" class="comment-item">
                    <div class="comment-avatar">
                        <el-image :src="item.avatarUrl"></el-image>
                    </div>
                    <div class="comment-content">
                        <!-- 评论者信息 -->
                        <b>{{ item.nickname }}： </b>
                        <span>{{ item.content }}</span>
                        <!-- 评论信息 -->
                        <div class="comment-info">
                            <i class="el-icon-time"></i><span>{{ item.time }}</span>
                            <el-button type="text" @click="handleReply(item.id)">回复</el-button>
                            <el-button type="text" style="color: red" @click="del(item.id)"
                                v-if="user.id === item.userId">删除</el-button>
                        </div>
                        <!-- 子评论列表 -->
                        <div v-if="item.children.length" class="sub-comment-list">
                            <div v-for="subItem in item.children" :key="subItem.id" class="sub-comment-item">
                                <b :class="{ 'pnickname': subItem.pnickname }">@{{ subItem.pnickname }}--</b>
                                <b>{{ subItem.nickname }}： </b>
                                <span>{{ subItem.content }}</span>
                                <div class="sub-comment-info">
                                    <i class="el-icon-time"></i><span>{{ subItem.time }}</span>
                                    <el-button type="text" @click="handleReply(subItem.id)">回复</el-button>
                                    <el-button type="text" style="color: red" @click="confirmDelete(subItem.id)"
                                        v-if="user.id === subItem.userId">删除</el-button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- 回复对话框 -->
        <el-dialog title="回复" :visible.sync="dialogFormVisible" width="50%">
            <el-form label-width="80px" size="small">
                <el-form-item label="回复内容">
                    <el-input type="textarea" v-model="commentForm.contentReply" autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false" size="small">取 消</el-button>
                <el-button type="primary" @click="save" size="small">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
const COMMENT_URL = "/echarts/comment";
export default {

    name: "ArticleDetail",
    data() {
        return {
            article: {},
            user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {},
            comments: [],
            commentForm: {},
            id: this.$route.query.id,
            dialogFormVisible: false,
        }
    },
    created() {
        this.load()
        this.fetchComments();
        this.loadComment()
       
    },
    methods: {
        load() {
            this.request.get("/article/" + this.id).then(res => {
                this.article = res.data
            });
        },
        loadComment() {
            this.request.get("/comment/tree/" + this.id).then(res => {
                this.comments = res.data
            })
        },
        save() {
            if (!this.user.id) {
                this.$message.warning("请登录后操作")
                return
            }
            this.commentForm.articleId = this.id
            if (this.commentForm.contentReply) {
                this.commentForm.content = this.commentForm.contentReply
            }
            this.request.post("/comment", this.commentForm).then(res => {
                if (res.code === '200') {
                    this.$message.success("评论成功")
                    this.commentForm = {}  // 初始化评论对象内容
                    this.loadComment()
                    this.fetchComments();
                    this.dialogFormVisible = false
                } else {
                    this.$message.error(res.msg)
                }
            })
        },
        del(id) {
            this.request.delete("/comment/" + id).then(res => {
                if (res.code === '200') {
                    this.$message.success("删除成功")
                    this.loadComment()
                } else {
                    this.$message.error("删除失败")
                }
            })
        },

        confirmDelete(id) {
            this.$confirm('确认删除该评论吗？', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                this.del(id)
            }).catch(() => {
                // 取消删除
            })
        },
        // 回复
        handleReply(pid) {
            this.commentForm = { pid: pid }
            this.dialogFormVisible = true
        },
        fetchComments() {
            this.request
                .get(COMMENT_URL,)
                .then((res) => {
                    console.log(res.data);
                })
                .catch((error) => {
                    console.error("Error fetching COMMENT:", error);
                    this.$message.error("获取评论列表失败！");
                });
        },
    },
    // 在页面加载完成后，设置定时器每隔一定时间执行loadComment()方法
    // mounted() {
    //     this.intervalId = setInterval(() => {
    //         this.loadComment();
    //     }, 1000); // 每隔1秒刷新一次，单位为毫秒
    // },
    beforeDestroy() {
        // 在页面销毁前清除定时器
        clearInterval(this.intervalId);
    },
}
</script>

<style scoped>
.article-detail {
    color: #666;
    margin: 20px 0;
}

.article-header {
    margin: 20px 0;
}

.article-title {
    font-size: 20px;
    color: #3F5EFB;
    cursor: pointer;
}

.article-info {
    font-size: 14px;
    margin-top: 10px;
}

.article-content {
    margin: 20px 0;
}

.comment-section {
    margin: 30px 0;
}

.comment-form {
    margin: 10px 0;
}

.comment-list {
    border-bottom: 1px solid orangered;
    padding: 10px 0;
    font-size: 20px;
}

.comment-item {
    border-bottom: 1px solid #ccc;
    padding: 10px 0;
    display: flex;
}

.comment-avatar {
    width: 100px;
    text-align: center;
}

.comment-content {
    flex: 1;
    font-size: 14px;
    padding: 5px 0;
    line-height: 25px;
}

.comment-info {
    display: flex;
    line-height: 20px;
    margin-top: 5px;
}

.sub-comment-list {
    padding-left: 200px;
    background-color: #f0f0f0;
    padding: 5px 20px;
}

.sub-comment-item {
    font-size: 14px;
    padding: 5px 0;
    line-height: 25px;
}

.sub-comment-info {
    display: flex;
    line-height: 20px;
    margin-top: 5px;
}

.comment-avatar {
    width: 50px;
    height: 50px;
    border-radius: 50%;
}

.pnickname {
    color: #3a8ee6;
}
</style>