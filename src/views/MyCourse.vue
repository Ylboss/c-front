<template>
    <div>
        <el-table :data="tableData" border stripe :header-cell-class-name="'headerBg'">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="id" label="ID" width="80"></el-table-column>
            <el-table-column prop="name" label="课程名称"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="times" label="课时"></el-table-column>
            <el-table-column prop="teacher" label="授课老师"></el-table-column>
            <el-table-column label="操作" width="200" align="center">
                <template slot-scope="scope">
                    <el-button type="search" @click="handleSearch(scope.row.id)" v-if="user.role === 'ROLE_USER'">查询文件<i class="el-icon-edit"></i></el-button>
                </template>
            </el-table-column>
        </el-table>

        <el-dialog title="查询文件" :visible.sync="dialogFormVisible" width="80%">
            <el-table :data="tableData1" border stripe :header-cell-class-name="'headerBg'" >
                <el-table-column prop="id" label="ID" width="80"></el-table-column>
                <el-table-column prop="name" label="文件名称"></el-table-column>
                <el-table-column prop="type" label="文件类型"></el-table-column>
                <el-table-column prop="size" label="文件大小(kb)"></el-table-column>
                <el-table-column prop="uploaderName" label="上传者"></el-table-column>
                <el-table-column label="下载">
                    <template slot-scope="scope">
                        <el-button type="primary" @click="download(scope.row.url)">下载</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </el-dialog>
    </div>
</template>

<script>
export default {
    name: "MyCourse",
    data() {
        return {
            tableData: [],
            tableData1: [],
            dialogFormVisible: false,
            user: JSON.parse(localStorage.getItem("user") || "{}"),
        }
    },
    created() {
        this.loadUserCourseIds();
    },
    methods: {
        async loadUserCourseIds() {
            try {
                const res = await this.request.get("/course/getUserCourseIds/" + this.user.id);
                if (res.data && Array.isArray(res.data)) {
                    const coursePromises = res.data.map(courseId => this.request.get("/course/" + courseId));
                    const courseResponses = await Promise.all(coursePromises);
                    this.tableData = courseResponses.map(res => res.data);
                } else {
                    console.error("Invalid courseIds data");
                }
            } catch (error) {
                console.error("Failed to load user course ids:", error);
            }
        },
        download(url) {
            window.open(url);
        },
        handleSearch(id) {
            this.$message({
                message: '查询文件中，请稍等...',
                type: 'success'
            });
            this.request.get("/file/byCourseId", {
                params: {
                    courseId: id,
                }
            }).then(res => {
                this.dialogFormVisible=true
                this.tableData1 = res.data;
            }).catch(error => {
                console.error("Failed to search files by course id:", error);
            });
        }
    }
}
</script>