<template>
    <div>
        <marquee style="color: cadetblue">管理员管理</marquee>
        <el-button type="info" style="margin-bottom: 10px;margin-right: 10px" @click="showAdd()">添加管理员</el-button>
        名字：<el-input placeholder="请输入名字" v-model="inputName" clearable style="width:140px;"></el-input>
        电话：<el-input placeholder="请输入电话" v-model="inputPhone" clearable style="width:140px;"></el-input>
        邮箱：<el-input placeholder="请输入邮箱" v-model="inputEmail" clearable style="width:140px;"></el-input>
        角色：<el-select v-model="hfValues" clearable  placeholder="请选择" style="width: 140px">
            <el-option v-for="item in raRoles" :value="item.value" :label="item.value">
            </el-option>
        </el-select>
        状态：<el-select v-model="raStateValue" clearable  placeholder="请选择" style="width: 140px">
                <el-option v-for="item in raState" :value="item.value" :label="item.text">
                </el-option>
            </el-select>
        <el-button type="primary" icon="el-icon-search" style="margin-left: 15px" @click="btnSeach()">搜索</el-button>
        <table>
            <tr>
                <th><input type="checkbox"/></th>
                <th>编号</th>
                <th>名字</th>
                <th>电话</th>
                <th>邮箱</th>
                <th>角色</th>
                <th>注册时间</th>
                <th>状态</th>
                <th>操作</th>
            </tr>
            <tr v-for="(item,index) in regsistAdmins">
                <td><input type="checkbox"/></td>
                <td>{{item.raId}}</td>
                <td>{{item.raName}}</td>
                <td>{{item.raPhone}}</td>
                <td>{{item.raEmail}}</td>
                <td>{{item.raRole}}</td>
                <td>{{item.raBirthday|timechange}}</td>
                <td v-if="item.raState==0">
                    <el-button type="primary" @click="btnState(1,item.raId)">启用</el-button>
                </td>
                <td v-else-if="item.raState==1">
                    <el-button type="primary" @click="btnState(0,item.raId)">注销</el-button>
                </td>
                <td>
                    <el-button type="danger" icon="el-icon-refresh" circle @click="rfPassword(item.raId)"></el-button>
                    <el-button type="danger" icon="el-icon-edit" circle @click="edit(item.raId,index)"></el-button>
                </td>
            </tr>
        </table>
        <!--分页-->
        <div class="block" style="margin-top: 15px">
            <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-sizes="[2, 4, 8]"
                    :page-size="pageSize"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="total">
            </el-pagination>
        </div>
        <el-dialog title="编辑信息" :visible.sync="dialogFormVisible">
            <el-form >
                <el-form-item label="名字" :label-width="formLabelWidth">
                    <el-input placeholder="请输入名字" v-model="raName"></el-input>
                </el-form-item>
                <el-form-item label="电话" :label-width="formLabelWidth">
                    <el-input placeholder="请输入电话号码" v-model="raPhone"></el-input>
                </el-form-item>
                <el-form-item label="邮箱" :label-width="formLabelWidth">
                    <el-input placeholder="请输入邮箱" v-model="raEmail"></el-input>
                </el-form-item>
                <el-form-item label="角色" :label-width="formLabelWidth">
                    <el-select v-model="hfValues" placeholder="请选择">
                        <el-option v-for="item in raRoles" :value="item.value" :label="item.value">
                        </el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="add()">添 加</el-button>
                <el-button type="primary" @click="update()">修 改</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
    import qs from 'qs'
    //axios.defaults.baseURL = 'https://api.example.com';
    export default {
        name: 'RegsistAdmin',
        data() {
            return {
                regsistAdmins:[], //全部管理员信息
                currentPage: 1, //保存当前页数
                pageSize:2,
                total:100,
                dialogFormVisible:false,
                formLabelWidth:'120px',
                raRoles:[{value:"超级管理员"},{value:"普通管理员"}],
                raState:[
                    {value:"1",text:"注销"},
                    {value:"0",text:"启用"}
                ],
                raStateValue:'',
                hfValues:'',
                raName:'',
                raEmail:'',
                raPhone:'',
                raId:'',
                inputName:'',
                inputPhone:'',
                inputEmail:''
            }
        },
        //过滤器
        filters:{
            timechange:function (value) {
                return new Date(value).toLocaleString();
            }
        },
        //挂载后
        mounted:function(){
            this.selectRegsistAdmin();
        },
        methods: {
            //查询管理员
            selectRegsistAdmin:function () {
                var that=this;
                var objectList=[];
                objectList.push(this.inputName);
                objectList.push(this.inputPhone);
                objectList.push(this.inputEmail);
                objectList.push(this.hfValues);
                objectList.push(this.raStateValue);
                //查询数量
                this.axios.post("http://localhost:8080/regsistAdmin/select_regsistAdmin_count",objectList).then(function (response) {
                    that.total=response.data.data;
                    console.log(that.total);
                    //axios简单写法
                    //查询数据
                    objectList.push(that.currentPage);
                    objectList.push(that.pageSize);
                    that.axios.post("http://localhost:8080/regsistAdmin/select_regsistAdmin",objectList).then(function (response) {
                        that.regsistAdmins=response.data.data;
                        console.log(response.data.data);
                    }).catch(function (error) {
                        console.log(error);
                    });
                }).catch(function (error) {
                   console.log(error);
                });
              //axios第二种写法
                /*this.axios({
                    url:'http://localhost:8080/regsistAdmin/select_regsistAdmin',
                    method:'post',
                    data:objectList
                }).then(function (response) {
                    console.log(response.data);
                }).catch(function (error) {
                    console.log(error);
                });*/
            },
            //每页数量改变
            handleSizeChange:function (val) {
                console.log(`每页 ${val} 条`);
                this.currentPage=1;
                this.pageSize=val;
                this.selectRegsistAdmin();
            },
            //每页条数改变
            handleCurrentChange:function (val) {
                console.log(`当前页: ${val}`);
                this.currentPage=val;
                this.selectRegsistAdmin();
            },
            //启用与注销
            btnState:function (raState,raId) {
                var that=this;
                this.$confirm('将对管理员状态进行操作, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.axios.get("http://localhost:8080/regsistAdmin/update_regsistAdmin_raState",{
                     params:{
                         raState:raState,
                         raId:raId
                     }
                     }).then(function (response) {
                         if(response.data.code==1){
                             that.$message("修改成功！");
                             that.selectRegsistAdmin();
                         }
                     }).catch(function (error) {
                         console.log(error);
                     })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消修改'
                    });
                });
            },
            //重置密码
            rfPassword:function (raId) {
                var that=this;
                this.$confirm('将要重置密码, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.axios.get("http://localhost:8080/regsistAdmin/update_regsistAdmin_RraPassword",{
                        params:{
                            raId:raId
                        }
                    }).then(function (response) {
                        if(response.data.code==1){
                            that.$message("重置密码成功！");
                            that.selectRegsistAdmin();
                        }
                    }).catch(function (error) {
                        console.log(error);
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消重置'
                    });
                });
            },
            //显示弹出添加框
            showAdd:function () {
                this.dialogFormVisible=true;
            },
            //添加
            add:function () {
                var that=this;
                //console.log(this.raName+","+this.raPhone+","+this.raEmail+","+this.hfValues);
                this.axios.post("http://localhost:8080/regsistAdmin/insert_regsistAdmin",{
                    raName:that.raName,
                    raPhone:that.raPhone,
                    raEmail:that.raEmail,
                    raRole:that.hfValues
                }).then(function (response) {
                    if(response.data.code==1){
                        that.$message("添加成功！");
                        that.selectRegsistAdmin();
                        that.dialogFormVisible=false;
                    }
                }).catch(function (error) {
                    console.log(error);
                });
            },
            //编辑
            edit:function (raId,index) {
                this.dialogFormVisible=true;
                this.raName=this.regsistAdmins[index].raName;
                this.raPhone=this.regsistAdmins[index].raPhone;
                this.raEmail=this.regsistAdmins[index].raEmail;
                this.hfValues=this.regsistAdmins[index].raRole;
                this.raId=raId;
            },
            //修改
            update:function () {
                var that=this;
                //console.log(this.raName+","+this.raPhone+","+this.raEmail+","+this.hfValues);
                this.axios.put("http://localhost:8080/regsistAdmin/update_regsistAdmin",{
                    raName:that.raName,
                    raPhone:that.raPhone,
                    raEmail:that.raEmail,
                    raRole:that.hfValues,
                    raId:that.raId
                }).then(function (response) {
                    if(response.data.code==1){
                        that.$message("修改成功！");
                        that.selectRegsistAdmin();
                        that.dialogFormVisible=false;
                    }
                }).catch(function (error) {
                    console.log(error);
                });
            },
            //搜索
            btnSeach:function () {
                this.selectRegsistAdmin();
            }
        },
        computed: {

        },
        watch:{
            raStateValue:function (newValue) {
                console.log(newValue);
            }
        }
    }

</script>

<style>
    table,th,td{
        border:1px solid lightgrey;
        border-collapse: collapse;
    }
    table{
        width: 100%;
        text-align: center;
    }
    tr{
        height: 40px;
    }
    tr td a{
        color: gainsboro;
    }
    th{
        color: yellowgreen;
    }
    td{
        color: #000000;
    }
</style>

