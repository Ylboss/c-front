<template>
    <div class="c-programming-introduction">
        <h2>C 语言基础知识介绍</h2>
        <div v-for="(item, index) in cItems" :key="index" :id="'c-item-' + index" class="c-item">
            <el-card>
                <template slot="header">
                    <h3>{{ item.title }}</h3>
                </template>
                <p>{{ item.description }}</p>
                <el-collapse v-if="item.code">
                    <el-collapse-item>
                        <template slot="title">
                            <i class="el-icon-s-grid"></i> 代码
                        </template>
                        <pre><code>{{ item.code }}</code></pre>
                    </el-collapse-item>
                </el-collapse>
            </el-card>
            <div>
                <el-radio-group v-model="direction">
                    <el-radio label="ltr">从左往右开</el-radio>
                    <el-radio label="rtl">从右往左开</el-radio>
                    <el-radio label="ttb">从上往下开</el-radio>
                    <el-radio label="btt">从下往上开</el-radio>
                </el-radio-group>
                <el-button @click="openDrawer" type="primary" style="margin-left: 16px;">
                    尝试一下！
                </el-button>
            </div>
        </div>
        <el-drawer title="C语言在线编译" :visible.sync="drawer" :direction="direction" @close="handleDrawerClose">
            <el-main>
                <el-input type="textarea" v-model="code" placeholder="请输入C语言代码" :rows="10"></el-input>
                <el-button type="primary" @click="compileCode">编译</el-button>
                <el-alert v-if="errorMessage" type="error" :description="errorMessage" show-icon></el-alert>
                <el-alert v-if="output" type="success" :description="output" show-icon></el-alert>
            </el-main>
        </el-drawer>
    </div>
</template>

<script>
import axios from 'axios';
export default {
    name: 'CLanguage',
    data() {
        return {
            code: '',
            errorMessage: '',
            output: '',
            drawer: false,
            direction: 'rtl',
            cItems: []
        };
    },
    async created() {
        await this.load();
    },
    methods: {
        async load() {
            try {
                const res = await this.request.get('/language');
                this.cItems = res.data;
                console.log('C Language items loaded:', res);
            } catch (error) {
                console.error('Error fetching challenges:', error);
            }
        },
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
        },
        openDrawer() {
            this.drawer = true;
        },
        handleDrawerClose() {
            this.$emit('close');
        }
    }
};
</script>

<style scoped>
.c-programming-introduction {
    padding: 20px;
}

.c-item {
    margin-bottom: 20px;
    border: 1px solid #ccc;
    padding: 10px;
    border-radius: 5px;
}

h3 {
    font-size: 20px;
    margin-bottom: 10px;
}

p {
    font-size: 16px;
}

pre {
    background-color: #f5f5f5;
    padding: 10px;
    border-radius: 5px;
}

code {
    font-family: monospace;
}
</style>