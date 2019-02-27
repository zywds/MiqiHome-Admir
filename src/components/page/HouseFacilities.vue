<template>
    <div>
        <marquee style="color: cadetblue">设施类型管理</marquee>
        <el-button type="info" style="margin-bottom: 10px" @click="showAdd()">添加类型</el-button>
        <table>
            <tr>
                <th><input type="checkbox"/></th>
                <th>编号</th>
                <th>名称</th>
                <th>操作</th>
            </tr>
            <tr v-for="item in houseFacilities">
                <td><input type="checkbox"/></td>
                <td>{{item.hfId}}</td>
                <td>{{item.hfName}}</td>
                <td>
                    <el-button type="danger" icon="el-icon-delete" circle @click="deletes()"></el-button>
                    <el-button type="danger" icon="el-icon-edit" circle @click="edit(item.hfId)"></el-button>
                </td>
            </tr>
        </table>
        <el-dialog title="编辑信息" :visible.sync="dialogFormVisible">
            <el-form >
                <el-form-item label="编号" :label-width="formLabelWidth">
                    <!-- <el-input  v-model="this.rmId" autocomplete="off"></el-input>-->
                    <el-input placeholder="请输入编号" disabled="disabled" v-model="hfId"></el-input>
                </el-form-item>
                <el-form-item label="名称" :label-width="formLabelWidth">
                    <el-input placeholder="请输入名称" v-model="hfName"></el-input>
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
    export default {
        name: "HouseFacilities",
        data(){
            return{
                houseFacilities:[],
                dialogFormVisible:false,
                formLabelWidth: '120px',
                hfId:'',
                hfName:'',
                disabled:true
            }
        },
        //挂载完成执行查询操作
        mounted() {
            this.selectHouseFacilities();
        },
        methods:{
            selectHouseFacilities:function () {
                var that=this;
                this.axios.get("http://localhost:8080/houseFacilitiesReflect/select_houseFacilities").then(function (response) {
                    that.houseFacilities=response.data.data;
                    console.log(that.houseFacilities);
                }).catch(function (error) {
                    console.log(error);
                });
            },
            //编辑
            edit:function (hfId) {
                this.hfId=this.houseFacilities[hfId-1].hfId;
                this.hfName=this.houseFacilities[hfId-1].hfName;
                this.dialogFormVisible=true;
            },
            //修改
            update:function () {
                var that=this;
                var datas=qs.stringify({
                    hfName:this.hfName,
                    hfId:this.hfId
                });
                this.axios.put("http://localhost:8080/houseFacilitiesReflect/update_houseFacilities?"+datas).then(function (response) {
                   that.selectHouseFacilities();
                   that.dialogFormVisible=false;
                   if(response.data.code==1){
                       that.$message("修改成功！");
                   }
                }).catch(function (error) {
                    console.log(error);
                });
            },
            //删除
            deletes:function () {
                this.$message("暂不可删除!");
            },
            //弹出添加框
            showAdd:function () {
                this.dialogFormVisible=true;
            },
            //添加
            add:function(){
                var that=this;
                this.axios.post("http://localhost:8080/houseFacilitiesReflect/insert_houseFacilities",
                    qs.stringify({
                        hfName:that.hfName
                    })
                ).then(function(response){
                    that.selectHouseFacilities();
                    that.dialogFormVisible=false;
                    if(response.data.code==1){
                        that.$message("添加成功！");
                    }
                }).catch(function(error){
                    console.log(error);
                });
            }
        }
    }
</script>

<style scoped>
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
