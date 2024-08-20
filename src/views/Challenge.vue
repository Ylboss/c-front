<template>
    <!--     页面主体       -->
    <div>
        <!--        搜索部分        -->
        <div class="search-container">
            <el-input class="search-input" placeholder="请输入标题" suffix-icon="el-icon-search" v-model="title"></el-input>
            <el-button class="search-btn" type="primary" @click="load">搜索</el-button>
            <el-button class="reset-btn" type="warning" @click="reset">重置</el-button>
        </div>
        <!--      表格外部操作部分          -->
        <div class="table-operations">
            <el-button class="add-btn" type="primary" @click="handleAdd">新增 <i class="el-icon-circle-plus-outline"></i></el-button>
            <el-popconfirm class="delete-confirm" confirm-button-text='确定' cancel-button-text='我再想想' icon="el-icon-info"
                icon-color="red" title="您确定批量删除这些数据吗？" @confirm="delBatch">
                <el-button class="delete-btn" type="danger" slot="reference">批量删除 <i class="el-icon-remove-outline"></i></el-button>
            </el-popconfirm>
        </div>

        <!--        表格内部操作部分        -->
        <el-table :data="tableData" border stripe :header-cell-class-name="'headerBg'"
            @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="id" label="ID" width="80"></el-table-column>
            <el-table-column prop="title" label="标题" width="150px"></el-table-column>
            <el-table-column prop="description" label="题目详细" width="150px"></el-table-column>
            <el-table-column prop="star" label="题目难度" width="150px">
                <template slot-scope="scope">
                    <el-rate :value="scope.row.star" :max="5" disabled></el-rate>
                </template>
            </el-table-column>
            <el-table-column prop="result" label="题目结果" width="150px"></el-table-column>
            <el-table-column prop="uploaderName" label="上传人" width="150px"></el-table-column>
            <el-table-column label="操作" width="222px" align="center">
                <template slot-scope="scope" v-if="user.username === scope.row.uploaderName">
                    <el-button type="success"  @click="handleEdit(scope.row)">编辑 <i class="el-icon-edit"></i></el-button>
                    <el-popconfirm class="delete-confirm" confirm-button-text='确定' cancel-button-text='我再想想' icon="el-icon-info"
                        icon-color="red" title="您确定删除吗？" @confirm="del(scope.row.id)">
                        <el-button class="delete-btn" type="danger" slot="reference">删除 <i class="el-icon-remove-outline"></i></el-button>
                    </el-popconfirm>
                </template>
            </el-table-column>
        </el-table>

        <!--       翻页与页码部分         -->
        <div class="pagination-container">
            <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="pageNum"
                :page-sizes="[2, 5, 10, 20]" :page-size="pageSize" layout="total, sizes, prev, pager, next, jumper"
                :total="total">
            </el-pagination>
        </div>

        <el-dialog title="练习题信息" :visible.sync="dialogFormVisible" width="30%">
            <el-form label-width="80px" size="small">
                <el-form-item label="标题">
                    <el-input v-model="form.title" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="题目详情">
                    <el-input type="textarea" v-model="form.description" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="题目难度">
                    <el-rate v-model="form.star" :max="5" style="margin-top:10px;"></el-rate>
                </el-form-item>
                <el-form-item label="题目结果">
                    <el-input v-model="form.result" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="上传人">
                    <el-input v-model="this.user.username" disabled autocomplete="off"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="save">确 定</el-button>
            </div>
        </el-dialog>

    </div>
</template>

<!--页面数据与动作Js代码-->
<script>
export default {
    name: "User",
    data() {
        return {
            tableData: [],
            total: 0,
            uploaderName:'',
            pageNum: 1,
            pageSize: 10,
            title: "",
            form: {},
            dialogFormVisible: false,
            multipleSelection: [],
            roles: [],
            courses: [],
            vis: false,
            stuCourses: [],
            stuVis: false,
            star: 0,
            user: JSON.parse(localStorage.getItem('user') || '{}'),
        }
    },
    // 请求分页查询数据
    created() {
        this.load()
    },
    methods: {
        // 将数据库查询操作封装
        load() {
            const params = {
                pageNum: this.pageNum,
                pageSize: this.pageSize,
                title: this.title,
            };
            this.request.get("/challenge/page", { params }).then(res => {
                console.log(res)
                this.tableData = res.data.records
                this.total = res.data.total
            });
        },
        save() {
            this.form.uploaderName = this.user.username; // 将当前用户的用户名赋给上传人字段
            this.request.post("/challenge", this.form).then(res => {
                if (res) {
                    this.$message.success("保存成功")
                    this.dialogFormVisible = false
                    this.load()
                } else {
                    this.$message.error("保存失败")
                }
            })
        },
        lookCourse(courses) {
            this.courses = courses
            this.vis = true
        },
        lookStuCourse(stuCourses) {
            this.stuCourses = stuCourses
            this.stuVis = true
        },
        handleAdd() {
            this.dialogFormVisible = true
            this.form = {}
        },
        handleEdit(row) {
            this.form = row
            this.dialogFormVisible = true
        },
        del(id) {
            this.request.delete("/challenge/" + id).then(res => {
                if (res) {
                    this.$message.success("删除成功")
                    this.load()
                } else {
                    this.$message.error("删除失败")
                }
            })
        },
        handleSelectionChange(val) {
            console.log(val)
            this.multipleSelection = val
        },
        delBatch() {
            let ids = this.multipleSelection.map(v => v.id)  // [{}, {}, {}] => [1,2,3]
            this.request.post("/challenge/del/batch", ids).then(res => {
                if (res) {
                    this.$message.success("批量删除成功")
                    this.load()
                } else {
                    this.$message.error("批量删除失败")
                }
            })
        },
        reset() {
            this.title = ""
            this.description = ""
            this.load()
        },
        // 动态分页请求
        handleSizeChange(pageSize) {
            console.log(pageSize)
            this.pageSize = pageSize
            this.load()
        },
        handleCurrentChange(pageNum) {
            console.log(pageNum)
            this.pageNum = pageNum
            this.load()
        },
    }
}
</script>

<!--表格头部样式-->
<style>
.search-container {
    margin: 10px 0;
}

.search-input {
    width: 200px;
}

.search-btn {
    margin-left: 5px;
}

.reset-btn {
    margin-left: 5px;
}

.table-operations {
    margin: 10px 0;
}

.add-btn {
    margin-right: 5px;
}

.delete-confirm {
    margin-left: 5px;
}

.delete-btn {
    margin-left: 5px;
}

.pagination-container {
    padding: 10px 0;
}

.headerBg {
    background: #eee !important;
}
</style>