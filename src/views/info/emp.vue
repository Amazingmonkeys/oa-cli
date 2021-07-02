<template>
    <div class="emp">
        <div class="my-nav">
            <el-breadcrumb separator-class="el-icon-arrow-right">
                <el-breadcrumb-item :to="{path: '/home'}">主页</el-breadcrumb-item>
                <el-breadcrumb-item>信息管理</el-breadcrumb-item>
                <el-breadcrumb-item>员工信息</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="my-query">
            <el-form :inline="true" :model="query" :rules="queryRules" size="mini" ref="queryForm">
                <el-form-item label="状态">
                    <el-select v-model="query.e_status" style="width: 128px;" @change="getPage(1)">
                        <el-option label="全部" value=""></el-option>
                        <el-option label="未确定" value="0"></el-option>
                        <el-option label="在职" value="1"></el-option>
                        <el-option label="已离职" value="2"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="部门">
                    <el-select v-model="query.d_id" style="width: 128px;" @change="getPage(1)">
                        <el-option label="全部" value=""></el-option>
                        <el-option v-for="dep in depList" :key="dep.d_id" :label="dep.d_name" :value="dep.d_id"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="性别">
                    <el-select v-model="query.e_sex" style="width: 128px;" @change="getPage(1)">
                        <el-option label="全部" value=""></el-option>
                        <el-option label="男" value="男"></el-option>
                        <el-option label="女" value="女"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="工号">
                    <el-input v-model="query.e_id" style="width: 128px;"></el-input>
                </el-form-item>
                <el-form-item label="姓名">
                    <el-input v-model="query.e_name" style="width: 128px;"></el-input>
                </el-form-item>
                <el-form-item label="备注">
                    <el-input v-model="query.e_remark" style="width: 128px;"></el-input>
                </el-form-item>
                <el-form-item label="出生日期">
                    <el-date-picker v-model="query.e_birth_start" type="date" placeholder="开始日期" value-format="yyyy-MM-dd" style="width: 128px;"></el-date-picker>
                    <span style="display: inline-block; width: 20px; text-align: center;">-</span>
                    <el-date-picker v-model="query.e_birth_end" type="date" placeholder="结束日期" value-format="yyyy-MM-dd" style="width: 128px;"></el-date-picker>
                </el-form-item>
                <div style="display: inline-block;">
                    <el-form-item label="月薪" prop="e_sal_start">
                        <el-input v-model.number="query.e_sal_start" style="width: 128px;" placeholder="最低月薪"></el-input>
                    </el-form-item>
                    <span style="display: inline-block; width: 20px; text-align: center;">-</span>
                    <el-form-item prop="e_sal_end">
                        <el-input v-model.number="query.e_sal_end" style="width: 128px;" placeholder="最高月薪"></el-input>
                    </el-form-item>
                </div>
                <div>
                    <el-form-item>
                        <el-button type="primary" icon="el-icon-search" @click.prevent="getPage(1)">查询</el-button>
                        <el-button @click="openAddDialog" type="success" icon="el-icon-plus">添加</el-button>
                        <el-button v-if="query.e_status == '0'" type="danger" icon="el-icon-delete" @click="execDeleteMulti">删除</el-button>
                    </el-form-item>
                </div>
            </el-form>
        </div>
        <div class="my-data">
            <el-table :data="page.rows" height="100%" @selection-change="handleSelectionChange">
                <el-table-column type="selection" width="55"></el-table-column>
                <el-table-column prop="e_id" label="工号"></el-table-column>
                <el-table-column prop="e_name" label="姓名">
                <template slot-scope="empPhotoShow">
                    <img v-if="empPhotoShow.row.e_photo" :src="$baseUrl+'info/emp/photo?_t='+new Date().getTime()+'&e_id='+empPhotoShow.row.e_id" style="height:30px;width:30px;" />
                    <span>{{empPhotoShow.row.e_name}}</span>
                </template>
                </el-table-column>
                <el-table-column prop="e_sex" label="性别"></el-table-column>
                <el-table-column prop="e_birth" label="出生日期"></el-table-column>
                <el-table-column prop="d_name" label="部门"></el-table-column>
                <el-table-column prop="e_salary" label="月薪"></el-table-column>
                <el-table-column prop="e_remark" label="备注"></el-table-column>
                <el-table-column prop="e_status_name" label="状态"></el-table-column>
                <el-table-column label="操作" width="260">
                    <template slot-scope="empLine">
                        <el-button size="mini" v-if="empLine.row.e_status == 0" @click = "openEditDialog(empLine.row)">编辑</el-button>
                        <el-button size="mini" v-if="empLine.row.e_status == 0" type="danger" @click = "execDelete(empLine.row)">删除</el-button>
                        <el-button size="mini" v-if="empLine.row.e_status == 1" type="warning">离职</el-button>
                        <el-button size="mini" v-if="empLine.row.e_status != 2" @click = "openPhotoDialog(empLine.row)">上传头像</el-button>
                        <el-button size="mini" v-if="empLine.row.e_status == 0" type="success">确定</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </div>
        <div class="my-page">
            <el-pagination 
            :page-size.sync="query.pageSize"
            :current-page.sync="query.pageNum"
            :total="page.total"
            layout="sizes, prev, pager, next, jumper, ->, total"
            :page-sizes="[3, 5, 10, 15, 20, 30]"
            @size-change="getPage(1)"
            @current-change="getPage(query.pageNum)">
            </el-pagination>
        </div>
        <el-dialog title="添加员工" :append-to-body="true" :visible.sync="dialogAddVisible"  @close="resetAddForm">
            <el-form :model="addObj" ref="addForm">
                <el-form-item label="员工姓名" label-width="100px">
                    <el-input v-model="addObj.e_name"></el-input>
                </el-form-item>
                <el-form-item label="员工性别" label-width="100px">
                    <el-radio v-model="addObj.e_sex" label="男">男</el-radio>
                    <el-radio v-model="addObj.e_sex" label="女">女</el-radio>
                </el-form-item>
                <el-form-item label="出生日期" label-width="100px">
                    <el-date-picker value-format="yyyy-MM-dd" v-model="addObj.e_birth" type="date" placeholder="选择日期"></el-date-picker>
                </el-form-item>
                <el-form-item label="所属部门" label-width="100px">
                    <el-select v-model="addObj.d_id">
                        <el-option label="请选择" value=""></el-option>
                        <el-option v-for="i in depList" :key="i.d_id" :label="i.d_name" :value="i.d_id"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="月薪" label-width="100px">
                    <el-input v-model.number="addObj.e_salary"></el-input>
                </el-form-item>
                <el-form-item label="备注" label-width="100px">
                    <el-input type="textarea" :rows="3" placeholder="请输入内容" v-model="addObj.e_remark"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogAddVisible = false">取消</el-button>
                <el-button type="primary" @click="execAdd">提交</el-button>
            </div>
        </el-dialog>
        <el-dialog title="编辑员工" :append-to-body="true" :visible.sync="dialogEditVisible">
            <el-form :model="editObj" ref="editForm">
                <el-form-item label="员工姓名" label-width="100px">
                    <el-input v-model="editObj.e_name"></el-input>
                </el-form-item>
                <el-form-item label="员工性别" label-width="100px">
                    <el-radio v-model="editObj.e_sex" label="男">男</el-radio>
                    <el-radio v-model="editObj.e_sex" label="女">女</el-radio>
                </el-form-item>
                <el-form-item label="出生日期" label-width="100px">
                    <el-date-picker value-format="yyyy-MM-dd" v-model="editObj.e_birth" type="date" placeholder="选择日期"></el-date-picker>
                </el-form-item>
                <el-form-item label="所属部门" label-width="100px">
                    <el-select v-model="editObj.d_id">
                        <el-option label="请选择" value=""></el-option>
                        <el-option v-for="i in depList" :key="i.d_id" :label="i.d_name" :value="i.d_id"></el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="月薪" label-width="100px">
                    <el-input v-model.number="editObj.e_salary"></el-input>
                </el-form-item>
                <el-form-item label="备注" label-width="100px">
                    <el-input type="textarea" :rows="3" placeholder="请输入内容" v-model="editObj.e_remark"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogEditVisible = false">取消</el-button>
                <el-button type="primary" @click="execEdit">提交</el-button>
            </div>
        </el-dialog>
        <el-dialog title="上传头像" :append-to-body="true" :visible.sync="dialogPhotoVisible">
            <el-upload :with-credentials="true" :headers="{'X-Requested-With':'XMLHttpRequest'}"
            :http-request="myUpload" :on-success="uploadPhotoSuccess" :action="$baseUrl+'info/emp/photo'" :data="{e_id: uploadEmp.e_id}" :show-file-list="false" class="avatar-uploader">
                <img v-if="photoUrl" :src="photoUrl" class="my-photo" />
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
            </el-upload>
        </el-dialog>
    </div>
</template>

<script>
export default {
    data(){
        const CHECKNUM = (rule, value, callback) => {
        if (!value) {
            return callback(new Error('不能为空'));
        } else if (!Number.isInteger(value)) {
            callback(new Error('请输入数字值'));
        } else {
            callback();
        }
      };

        return{
            queryRules: {
                //e_sal_start: [{ validator: CHECKNUM, trigger: 'blur' }],
                //e_sal_end: [{ validator: CHECKNUM, trigger: 'blur' }]
            },
            query: {
                e_status: '', //这3个是默认值 全部
                d_id: '',
                e_sex: '',
                pageSize: 10, //每页记录数
                pageNum: 1 //页码
            },
            depList: [],
            //list: [] //员工清单数据
            page: {},
            dialogAddVisible: false,
            addObj: {
                d_id: '',
            }, //新增员工数据
            editObj: {
            }, 
            dialogEditVisible: false,
            selections: [], // 当前选中的数据
            dialogPhotoVisible: false,
            uploadEmp: {}, //正在上传头像的员工
            photoUrl: '' //刚刚上传成功的图片地址
        }
    },

    methods:{
        /*getDepList(){
            this.$myGet('info/emp/dep')
            .then((data)=>{
                this.depList = data;
            })
        },*/
        getDepList(){
            this.$axios.get('info/emp/dep')
            .then(dep=>{
                this.depList = dep.data;
            })
        },
        getPage(pageNum){
            this.$refs['queryForm'].validate((ok)=>{
                if(ok) {
                    this.query.pageNum = pageNum;
                    /*this.$myGet("info/emp", {params: this.query})
                    .then((data)=>{
                        this.page = data;
                        this.query.pageSize = this.page.pgSize;
                        this.query.pageNum = this.page.curr;
                        console.log(this.page.pgSize);
                    })*/
                    this.$axios.get("info/emp", {params: this.query})
                    .then(e=>{
                        this.page = e.data;
                        this.query.pageSize = this.page.pgSize;
                        this.query.pageNum = this.page.curr;
                        console.log(this.page.pgSize);
                    })
                } else{
                    this.$message.error('查询条件不正确');
                }
            })
        },
        openAddDialog(){
            this.dialogAddVisible = true;
        },
        execAdd(){ //执行新增
            this.$confirm('您确定新增吗？','提示',{
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(()=>{
                this.$axios.post('info/emp', this.addObj) //以payload格式传参
                .then(()=>{
                    this.dialogAddVisible = false;
                    this.getPage(this.page.last + 1);
                });
            }).catch(()=>{
                this.$message({
                    type: 'info',
                    message: '已取消'
                });
            });
        },

        resetAddForm(){
            this.addObj = {d_id: ''};
        },

        openEditDialog(oldEmp){
            this.editObj = {...oldEmp}; //将oldEmp对象的属性依次赋值给editObj
            this.dialogEditVisible = true;
        },
        execEdit(){ //执行编辑
            this.$confirm('您确定编辑吗？','提示',{
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(()=>{
                this.$axios.put('info/emp', this.editObj) //以payload格式传参
                .then(()=>{
                    this.dialogEditVisible = false;
                    this.getPage(this.page.curr);
                });
            }).catch(()=>{
                this.$message({
                    type: 'info',
                    message: '已取消'
                });
            });
        },
        execDelete(oldEmp){
            this.$confirm('您确定删除吗？','提示',{
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(()=>{
                this.$axios.delete('info/emp/'+oldEmp.e_id) //以path variable格式传参
                .then(()=>{
                    this.getPage(this.page.curr);
                });
            }).catch(()=>{
                this.$message({
                    type: 'info',
                    message: '已取消'
                });
            });
        },
        //选项改变
        handleSelectionChange(selections){
            this.selections = selections;
        },
        execDeleteMulti(){
            if(this.selections.length == 0){
                this.$message.error('您没有选择任何数据');
                return;
            }
            let ids = [];
            for(let i = 0;i<this.selections.length;i++){
                ids.push(this.selections[i].e_id);
            }
            this.$confirm('您确定删除吗？','提示',{
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(()=>{
                this.$axios.delete('info/emp', {data: ids}) //以payload格式传参
                .then(()=>{
                    this.getPage(this.page.curr);
                });
            }).catch(()=>{
                this.$message({
                    type: 'info',
                    message: '已取消'
                });
            });
        },
        //打开上传头像窗口
        openPhotoDialog(emp){
            this.photoUrl='';
            this.uploadEmp = emp;
            this.dialogPhotoVisible = true;
        },
        uploadPhotoSuccess(result){
            this.getPage(this.page.curr);
            this.photoUrl = this.$baseUrl + "info/emp/photo?_t="+new Date().getTime()+"&e_id="+this.uploadEmp.e_id;
            this.$message.success(result.message);
        },
        myUpload(c){
            var fd = new FormData();
            fd.append('file', c.file);
            fd.append('e_id',this.uploadEmp.e_id);
            let config = {
                headers: {
                    'Content-Type': 'multipart/form-data'
                }
            }
            this.$axios.post('http://localhost/info/emp/photo', fd,config).then( res => {
            console.log('res');
            this.dialogPhotoVisible = false;
            this.getPage(this.page.curr);
            }).catch(res => {
                console.log(res)
            });
            
        }
    },

    mounted(){
        this.getDepList();
        this.getPage(1);
    }
}
</script>

<style scoped>
.emp{
    position: relative;
    background-color: antiquewhite;
    height: 100%;
}
.my-nav{
    position: absolute;
    left: 0px;
    top: 0px;
    width: 100%;
    height: 15px;
}
.my-query{
    position: absolute;
    left: 10px;
    top: 30px;
    right: 0px;
    height: 50px;
    height: 220px;
    text-align: left;
}
.my-data{
    position: absolute;
    left: 10px;
    right: 10px;
    top: 170px;
    bottom: 40px;
}
.my-page{
    position: absolute;
    left: 10px;
    right: 10px;
    bottom: 0px;
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
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
}
.avatar {
    width: 178px;
    height: 178px;
    display: block;
}
.my-photo {
    width: 200px;
    height: 200px;
}
</style>