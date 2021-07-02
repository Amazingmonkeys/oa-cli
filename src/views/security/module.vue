<template>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right" style="margin-bottom: 10px;">
            <el-breadcrumb-item :to="{path: '/home'}">欢迎页</el-breadcrumb-item>
            <el-breadcrumb-item :to="{path: '/home'}">安全管理</el-breadcrumb-item>
            <el-breadcrumb-item :to="{path: '/home'}">权限管理</el-breadcrumb-item>
        </el-breadcrumb>
        <el-table :data="userList" style="width: 100%">
            <el-table-column prop="u_id" label="账号"></el-table-column>
            <el-table-column prop="u_status" label="状态">
                <template slot-scope="user">
                    {{user.row.u_status==1?"已启用":"已禁用"}}
                </template>
            </el-table-column>
            <el-table-column label="操作">
                <template slot-scope="user">
                    <el-button @click="unuse(user.row)" v-if="user.row.u_status==1" plain>禁用</el-button>
                    <el-button @click="use1(user.row)" v-if="user.row.u_status==0" type="success" plain>启用</el-button>
                    <el-button @click="openModuleDialog(user.row)" v-if="user.row.u_status==1" type="primary" plain>权限</el-button>
                </template>
            </el-table-column>
        </el-table>

        <!--权限对话框-->
        <el-dialog :append-to-body="true" :title="'用户' + moduleUser.u_id + '的权限'" :visible.sync="dialogFormVisible">
            {{menuIdList}}
                <div v-for="menu in menuList" :key="menu.menuId" style="text-align: left; margin: 10px;">
                    <div>{{menu.menuName}}</div>
                    <div style="margin-top: 10px; padding-left: 20px;">
                        <el-checkbox-group v-model="menuIdList">
                            <el-checkbox style="margin: 5px;" v-for="subMenu in menu.subMenus" :key="subMenu.menuId" :label="subMenu.menuId">{{subMenu.menuName}}</el-checkbox>
                        </el-checkbox-group>
                    </div>
                </div>
            <div slot="footer">
                <el-button @click="dialogFormVisible = false">取消</el-button>
                <el-button type="primary" @click="setModule">确定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
export default {
    data(){
        return {
            userList: [],
            dialogFormVisible: false, //对话框是否显示
            menuIdList: [], //选中的权限值
            menuList: [], // 所有权限
            moduleUser: {} //当前正在处理授权的用户
        }
    },

    methods:{
        getUserList(){
            this.$axios.get('security/module/user')
            .then(ul=>{
                this.userList = ul.data;
            });
        },

        //获得所有的权限
        getMenuList(){
            this.$axios.get('security/module')
            .then(ml=>{
                this.menuList = ml.data;
            })
        },

        //打开权限窗口
        openModuleDialog(data){
            this.moduleUser = data;
            this.$axios.get('security/module/' + data.u_id)
            .then(m=>{
                this.menuIdList = m.data; //获得用户的权限编号
                this.dialogFormVisible = true; //打开窗口
            })
        },

        //禁用用户
        unuse(data){ //data在上面的实参是user.row
            this.$confirm('您确实要禁用该用户吗？', '提示', {
                confirmButtonText: '确认禁用',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(()=>{ //正向操作后的逻辑
                this.$axios.put('security/module/unuse/' + data.u_id)
                .then(()=>{
                    this.getUserList(); //刷新数据
                })
            }).catch(()=>{ //负向操作后的逻辑
                this.$message('操作已取消'); 
            })
        },

        //启用用户
        use1(data){ //data在上面的实参是user.row
            this.$confirm('您确实要启用该用户吗？', '提示', {
                confirmButtonText: '确认启用',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(()=>{ //正向操作后的逻辑
                this.$axios.put('security/module/use/' + data.u_id)
                .then(()=>{
                    this.getUserList(); //刷新数据
                })
            }).catch(()=>{ //负向操作后的逻辑
                this.$message('操作已取消'); 
            })
        },

        //设置权限
        setModule(){
            this.$confirm('您确实要更新用户权限吗？', '提示', {
                confirmButtonText: '确认',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(()=>{ //正向操作后的逻辑
                //this.$myPut方法的第二个参数表示向服务器传送的数据，是以payload方式发送的
                this.$axios.put('security/module/setModule/' + this.moduleUser.u_id, this.menuIdList);
                this.dialogFormVisible = false;
            }).catch(()=>{ //负向操作后的逻辑
                this.$message('操作已取消'); 
            })
        }
    },

    mounted(){
        this.getUserList();
        this.getMenuList(); //获取所有权限
    }
}
</script>

<style scoped>

</style>