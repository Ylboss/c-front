<!--
 * @Author: XUNYUYU
 * @Date: 2024-04-25 19:46:15
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-05-31 18:40:43
 * @FilePath: \graduation_design\src\views\front\CChallenge.vue
-->
<template>
    <div class="c-programming-challenge">
        <h2>C 语言编程练习挑战</h2>
        <el-card>
            <div slot="header" class="challenge-header">
                <h3>{{ currentChallenge.title }}</h3>
            </div>
            <div class="challenge-description">
                <p>{{ currentChallenge.description }}</p>
            </div>
            <el-main>
                <el-input type="textarea" v-model="code" placeholder="请输入C语言代码" :rows="10"></el-input>
                <el-button type="primary" @click="compileCode">编译</el-button>
                <el-alert v-if="errorMessage" type="error" :description="errorMessage" show-icon></el-alert>
                <el-alert v-if="output" type="success" :description="output" show-icon></el-alert>
            </el-main>
        </el-card>

        <el-card>
            <div slot="header" class="challenge-header">
                <h3>挑战题目列表</h3>
            </div>
            <div class="search-bar">
                <el-input v-model="title" placeholder="输入题目进行搜索" clearable></el-input>
                <el-input v-model="star" placeholder="输入星级难度进行搜索" clearable></el-input>
                <el-input v-model="uploaderName" placeholder="输入上传人姓名进行搜索" clearable></el-input>
                <el-button type="success" @click="searchByUploaderName">搜索</el-button>
                <el-button type="warning" @click="reset">重置</el-button>
            </div>

            <el-table :data="challenges" style="width: 100%">
                <el-table-column label="标题" prop="title"></el-table-column>
                <el-table-column label="描述" prop="description"></el-table-column>
                <el-table-column label="题目难度">
                    <template v-slot="scope">
                        <el-rate :value="scope.row.star" :max="5" disabled style="margin-top:10px;"></el-rate>
                    </template>
                </el-table-column>
                <el-table-column label="上传人" prop="uploaderName"></el-table-column>
                <el-table-column label="操作">
                    <template v-slot="scope">
                        <el-button type="text" @click="editChallenge(scope.row)">上传题目</el-button>
                    </template>
                </el-table-column>
            </el-table>

        </el-card>
    </div>
</template>

<script>
import axios from 'axios';
export default {
    name: 'CChallenge',
    data() {
        return {
            code: '',
            errorMessage: '',
            output: '',
            uploaderName: '',
            title: '',
            star: '',
            challenges: [],
            currentChallenge: {},
            challengeForm: {
                code: '',
            },
            user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {}
        };
    },
    async created() {
        await this.loadChallenges();
        this.currentChallenge = this.challenges[0];
    },
    methods: {
        async searchByUploaderName() {
            // 执行搜索逻辑
            try {
                const res = await axios.get('http://localhost:9090/challenge/search', {
                    params: {
                        uploaderName: this.uploaderName,
                        title:this.title,
                        star:this.star,
                    }
                });
                this.challenges = res.data.data; // 假设后端返回的数据在data字段中
                console.log(res.data.data);
            } catch (error) {
                console.error('Error fetching challenges:', error);
            }
        },
        async loadChallenges() {
            try {
                const res = await axios.get('http://localhost:9090/challenge');
                this.challenges = res.data.data; // 假设后端返回的数据在data字段中
                console.log(res.data.data);
                this.currentChallenge = this.challenges[0]; // 设置第一个挑战为当前挑战
            } catch (error) {
                console.error('Error fetching challenges:', error);
            }
        },

        editChallenge(challenge) {
            this.currentChallenge = challenge;
        },

        async compileCode() {
            try {
                const res = await axios.post('http://localhost:9090/api/compile', { code: this.code });

                if (res.status === 200) {
                    this.output = res.data;
                    console.log("" + this.output);
                    console.log("类型", typeof (this.output))
                    console.log(this.currentChallenge.id)
                    // 假设从后端返回的数据中包含了挑战的结果
                    const challengeResult = this.currentChallenge.result;

                    // console.log(" challengeResult " + challengeResult);
                    // console.log("类型", typeof (challengeResult))
                    if (JSON.stringify(this.output) === JSON.stringify(challengeResult)) {
                        this.request.post("/challenge/userChallenge/" + this.currentChallenge.id + "/" + this.user.id).then(res => {
                            if (res.code === '200') {
                                this.$message.success("挑战成功")
                            } else {
                                this.$message.success(res.msg)
                            }
                        })
                        console.log("答案正确");
                    } else {
                        console.log("答案不对");
                    }
                    // 比较 output 和 challengeResult
                    // output 等于 challengeResult，可以修改 user_challenge 表中的 performance
                    // try {
                    //     const res = await axios.post('http://localhost:9090/user-challenge/updatePerformance', { performance: 'completed', userid: this.userid, challengeid: this.currentChallenge.id, output: this.output, challengeResult: challengeResult });
                    //     console.log('Performance updated:', res.data);
                    // } catch (error) {
                    //     console.error('Error updating performance:', error);
                    // }
                }

            } catch (error) {
                console.error(error);
                this.errorMessage = '编译出错，请检查你的代码或稍后重试。';
            }
        },
        reset() {
            this.uploaderName = ""
            this.title = ""
            this.star="0"
            this.loadChallenges()
        },

        deleteChallenge(index) {
            this.challenges.splice(index, 1);
        },
    },
    mounted() {
        this.loadChallenges();
    },
};
</script>

<style>
.challenge-header {
    padding: 10px;
    background-color: #f5f5f5;
}

.challenge-description {
    padding: 20px;
}

.el-card {
    margin: 20px;
}
.search-bar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
}

.search-bar el-input {
    flex: 1;
    margin-right: 10px;
}
</style>