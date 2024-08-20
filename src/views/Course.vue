<template>
    <div>
        <div style="margin: 10px 0">
            <el-input style="width: 200px" placeholder="请输入名称" suffix-icon="el-icon-search" v-model="name"></el-input>
            <el-input style="width: 200px" placeholder="请输入讲师" suffix-icon="el-icon-search"
                v-model="teacher"></el-input>
            <el-button class="ml-5" type="primary" @click="load">搜索</el-button>
            <el-button type="warning" @click="reset">重置</el-button>
        </div>
        <div style="margin: 10px 0">
            <el-button type="primary" @click="handleAdd"
                v-if="user.role === 'ROLE_TEACHER' || user.role === 'ROLE_ADMIN'">新增 <i
                    class="el-icon-circle-plus-outline"></i></el-button>
            <el-popconfirm class="ml-5" confirm-button-text='确定' cancel-button-text='我再想想' icon="el-icon-info"
                icon-color="red" title="您确定批量删除这些数据吗？" @confirm="delBatch">
                <el-button type="danger" slot="reference" v-if="user.role === 'ROLE_ADMIN'">批量删除 <i
                        class="el-icon-remove-outline"></i></el-button>
            </el-popconfirm>

        </div>
        <el-table :data="tableData" border stripe :header-cell-class-name="'headerBg'"
            @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="id" label="ID" width="80"></el-table-column>
            <el-table-column prop="name" label="课程名称" width="110"></el-table-column>
            <el-table-column prop="price" label="价格" width="110"></el-table-column>
            <el-table-column prop="times" label="课时" width="110"></el-table-column>
            <el-table-column prop="teacher" label="授课老师" width="110"></el-table-column>
            <!-- <el-table-column label="启用">
                <template slot-scope="scope">
                    <el-switch v-model="scope.row.state" active-color="#13ce66" inactive-color="#ccc"
                               @change="changeEnable(scope.row)"></el-switch>
                </template>
</el-table-column> -->
            <el-table-column label="操作" width="350" align="center">
                <template slot-scope="scope">
                    <el-button type="primary" @click="selectCourse(scope.row.id)"
                        v-if="user.role === 'ROLE_USER'">选课</el-button>
                    <el-upload action="http://localhost:9090/file/upload"
                        :data="{ uploaderName: user.username, purpose: '讲师上传课件' , courseId: scope.row.id}" :show-file-list="false"
                        :on-success="handleFileUploadSuccess" style="display: inline-block">
                        <el-button type="primary" class="ml-5" v-if="user.role === 'ROLE_TEACHER' && user.nickname == scope.row.teacher">上传课程文件 <i class="el-icon-top"></i></el-button>
                    </el-upload>
                    <el-button type="success" @click="handleEdit(scope.row)"
                        v-if="user.role === 'ROLE_TEACHER' && user.nickname == scope.row.teacher">编辑 <i
                            class="el-icon-edit"></i></el-button>
                    <el-popconfirm class="ml-5" confirm-button-text='确定' cancel-button-text='我再想想' icon="el-icon-info"
                        icon-color="red" title="您确定删除吗？" @confirm="del(scope.row.id)">
                        <el-button type="danger" slot="reference"
                            v-if="user.role === 'ROLE_TEACHER' && user.nickname == scope.row.teacher">删除 <i
                                class="el-icon-remove-outline"></i></el-button>
                    </el-popconfirm>
                </template>
            </el-table-column>
        </el-table>

        <div style="padding: 10px 0">
            <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="pageNum"
                :page-sizes="[2, 5, 10, 20]" :page-size="pageSize" layout="total, sizes, prev, pager, next, jumper"
                :total="total">
            </el-pagination>
        </div>

        <el-dialog title="课程信息" :visible.sync="dialogFormVisible" width="30%">
            <el-form label-width="80px" size="small">
                <el-form-item label="名称">
                    <el-input v-model="form.name" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input v-model="form.price" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="课时">
                    <el-input v-model="form.times" autocomplete="off"></el-input>
                </el-form-item>
                <!-- <el-form-item v-if="user.role === 'ROLE_TEACHER'" label="老师">
                    <span>{{ user.nickname }}</span>
                </el-form-item> -->
                <!-- <el-form-item  label="老师id">
                    <el-select clearable v-model="form.teacherid" placeholder="请选择">
                        <el-option v-for="item in teachers" :key="item.id" :label="item.nickname"
                                   :value="item.id"></el-option>
                    </el-select>
                </el-form-item> -->
                <el-form-item label="老师">
                    <el-select clearable v-model="form.teacher" placeholder="请选择">
                        <el-option v-for="item in teachers" :key="item.id" :label="item.nickname"
                            :value="item.nickname"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="save">确 定</el-button>
            </div>
        </el-dialog>

    </div>
</template>

<script>
export default {
    name: "Course",
    data() {
        return {
            form: {},
            tableData: [],
            name: '',
            multipleSelection: [],
            pageNum: 1,
            pageSize: 10,
            total: 0,
            teacher: '',
            dialogFormVisible: false,
            teachers: [],
            user: localStorage.getItem("user") ? JSON.parse(localStorage.getItem("user")) : {},
        }
    },
    created() {
        this.load()
    },
    methods: {
        load() {
            this.request.get("/course/page", {
                params: {
                    pageNum: this.pageNum,
                    pageSize: this.pageSize,
                    name: this.name,
                    teacher: this.teacher
                }
            }).then(res => {
                this.tableData = res.data.records
                console.log(this.tableData)
                this.total = res.data.total
            });
            this.request.get("/user/role/ROLE_TEACHER").then(res => {
                this.teachers = res.data
                console.log(this.teachers)
            })
        },
        
        selectCourse(courseId) {
            this.request.post("/course/userCourse/" + courseId + "/" + this.user.id).then(res => {
                if (res.code === '200') {
                    this.$message.success("选课成功")
                } else {
                    this.$message.success(res.msg)
                }
            })
        },
        // changeEnable(row) {
        //     this.request.post("/course/update", row).then(res => {
        //         if (res.code === '200') {
        //             this.$message.success("操作成功")
        //         }
        //     })
        // },
        del(id) {
            this.request.delete("/course/" + id).then(res => {
                if (res.code === '200') {
                    this.$message.success("删除成功")
                    this.load()
                } else {
                    this.$message.error("删除失败")
                }
            })
        },
        handleFileUploadSuccess(res) {
            console.log(res)
            this.$message.success("文件上传成功");
            this.load()
        },
        handleSelectionChange(val) {
            console.log(val)
            this.multipleSelection = val
        },
        delBatch() {
            let ids = this.multipleSelection.map(v => v.id)  // [{}, {}, {}] => [1,2,3]
            this.request.post("/course/del/batch", ids).then(res => {
                if (res.code === '200') {
                    this.$message.success("批量删除成功")
                    this.load()
                } else {
                    this.$message.error("批量删除失败")
                }
            })
        },
        save() {
            this.request.post("/course", this.form).then(res => {
                if (res) {
                    this.$message.success("保存成功")
                    this.dialogFormVisible = false
                    this.load()
                } else {
                    this.$message.error("保存失败")
                }
            })
        },
        reset() {
            this.name = ""
            this.teacher = ""
            this.load()
        },
        handleAdd() {
            this.dialogFormVisible = true
            this.form = {}
        },
        handleEdit(row) {
            this.form = row
            this.dialogFormVisible = true
        },
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
        download(url) {
            window.open(url)
        }
    }
}
</script>

<style scoped></style>