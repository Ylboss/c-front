<!--
 * @Author: XUNYUYU
 * @Date: 2024-05-18 00:29:56
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-05-18 01:42:36
 * @FilePath: \graduation_design\src\views\MyChallenge.vue
-->
<template>
    <div>
        <el-table :data="tableData" border stripe :header-cell-class-name="'headerBg'"
            >
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="id" label="ID" width="80"></el-table-column>
            <el-table-column prop="title" label="标题" width="300px"></el-table-column>
            <el-table-column prop="description" label="题目详细" width="300px"></el-table-column>
            <el-table-column prop="star" label="题目难度" width="150px">
                <template slot-scope="scope">
                    <el-rate :value="scope.row.star" :max="5" disabled></el-rate>
                </template>
            </el-table-column>
            <el-table-column prop="result" label="题目结果" width="300px"></el-table-column>
        </el-table>
    </div>
</template>

<script>
export default {
    name: "MyChallenge",
    data() {
        return {
            tableData: [],
            ChallengeIds: [],
            ChallengeId:"",
            userId: "",
            user: JSON.parse(localStorage.getItem("user") || "{}"),
        }
    },
    created() {
        this.load()
    },
    methods: {
        async load() {
            try {
                // 发送请求获取用户课程ID列表
                const res = await this.request.get("/challenge/getUserChallengeIds/" + this.user.id);
                // 将获取到的课程ID列表赋值给this.ChallengeIds
                this.ChallengeIds = res.data;
                // 打印课程ID列表
                console.log("ChallengeIds: ", this.ChallengeIds);
                // 打印课程ID列表的类型
                console.log("ChallengeIds type: ", typeof this.ChallengeIds);

                // 判断this.courseIds是否存在且为数组类型
                if (this.ChallengeIds && Array.isArray(this.ChallengeIds)) {
                    // 对每个课程ID发起请求获取课程信息，并将请求放入数组coursePromises中
                    const ChallengePromises = this.ChallengeIds.map(ChallengeId => this.request.get("/challenge/" + ChallengeId));
                    // 等待所有请求完成，并将结果存储在courseResponses中
                    const ChallengeResponses = await Promise.all(ChallengePromises);
                    // 将每个响应的数据提取出来，并赋值给this.tableData
                    this.tableData = ChallengeResponses.map(res => res.data);
                } else {
                    // 如果this.courseIds不是有效的数据，则打印错误信息
                    console.error("Invalid ChallengeIds data");
                }
            } catch (error) {
                // 捕获并打印错误信息
                console.error(error);
            }
        }
    }
}
</script>