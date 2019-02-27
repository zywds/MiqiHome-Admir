<template>
    <div>
        <marquee style="color: cadetblue">设施服务详细管理</marquee>
        <el-button type="info" style="margin-bottom: 10px" @click="showAdd()">添加详细</el-button>
        <el-select v-model="hfValue" placeholder="请选择">
            <el-option v-for="item in houseFacilities" v-bind:value="item.hfId" :label="item.hfName">
            </el-option>
        </el-select>
        <table>
            <tr>
                <th><input type="checkbox"/></th>
                <th>编号</th>
                <th>名称</th>
                <th>设施服务类型</th>
                <th>操作</th>
            </tr>
            <tr v-for="(item,index) in houseFacilitiesDatis">
                <td><input type="checkbox"/></td>
                <td>{{item.hfdId}}</td>
                <td>{{item.hfdName}}</td>
                <td>{{item.houseFacilities.hfName}}</td>
                <td>
                    <el-button type="danger" icon="el-icon-delete" circle @click="deletes()"></el-button>
                    <el-button type="danger" icon="el-icon-edit" circle @click="edit(index)"></el-button>
                </td>
            </tr>
        </table>
        <!--<div class="pagination">
            <el-pagination background @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
            </el-pagination>
        </div>-->
        <div class="block" style="margin-top: 15px">
            <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleCurrentChange"
                    :current-page="currentPage"
                    :page-sizes="[5, 10, 15]"
                    :page-size="pageSize"
                    layout="total, sizes, prev, pager, next, jumper"
                    :total="total">
            </el-pagination>
        </div>
        <el-dialog title="编辑信息" :visible.sync="dialogFormVisible">
            <el-form >
                <el-form-item label="编号" :label-width="formLabelWidth">
                    <!-- <el-input  v-model="this.rmId" autocomplete="off"></el-input>-->
                    <el-input placeholder="请输入编号" disabled="disabled" v-model="hfdId"></el-input>
                </el-form-item>
                <el-form-item label="名称" :label-width="formLabelWidth">
                    <el-input placeholder="请输入名称" v-model="hfdName"></el-input>
                </el-form-item>
                <el-form-item label="类型选择" :label-width="formLabelWidth">
                    <el-select v-model="hfValues" placeholder="请选择">
                        <el-option v-for="item in houseFacilities" v-bind:value="item.hfId" :label="item.hfName">
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
    export default {
        name: "HouseFacilitiesDetails",
        data(){
            return{
                hfValue:'居家',//下拉框选中的值
                houseFacilities:[], //所有设施类型值
                houseFacilitiesDatis:[],//所有类型详细值
                currentPage: 1, //保存当前页数
                pageSize:5,
                total:100,
                dialogFormVisible:false,
                formLabelWidth:'120px',
                hfValues:'',//修改框中下拉框的值
                hfdId:'',
                hfdName:''
            }
        },
        mounted() {
            //执行查询设施服务类型
            this.selectHouseFacilities();
            this.selectHousefacilitiesdetailsCount();//查询总数
            this.selectHouseFacilitiesDetails();
        },
        methods:{
            //查询设施服务类型
            selectHouseFacilities:function () {
                var that=this;
                console.log(that.hfValue);
                this.axios.get("http://localhost:8080/houseFacilitiesReflect/select_houseFacilities").then(function (response) {
                    that.houseFacilities=response.data.data;
                    console.log(that.houseFacilities);
                }).catch(function (error) {
                    console.log(error);
                });
            },
            //查询设施服务详细
            selectHouseFacilitiesDetails:function () {
                var hfValues="";
                if(this.hfValue=="居家"){
                    hfValues=1;
                }else{
                    hfValues=this.hfValue;
                }
                var that=this;
                this.axios.get("http://localhost:8080/houseFacilitiesReflect/select_housefacilitiesdetails",{
                    params:{
                        hfId:hfValues,
                        page:that.currentPage,
                        limit:that.pageSize
                    }
                }).then(function (response) {
                    that.houseFacilitiesDatis=response.data.data;
                    console.log(that.houseFacilitiesDatis);
                }).catch(function (error) {
                    console.log(error);
                });
            },
            //查询总数
            selectHousefacilitiesdetailsCount:function(){
                var hfValues="";
                if(this.hfValue=="居家"){
                    hfValues=1;
                }else{
                    hfValues=this.hfValue;
                }
                var that=this;
                this.axios.get("http://localhost:8080/houseFacilitiesReflect/select_housefacilitiesdetails_count",{
                    params:{
                        hfId:hfValues
                    }
                }).then(function (response) {
                    that.total=response.data.msg;
                    console.log(that.total);
                }).catch(function (error) {
                    console.log(error);
                });
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
                this.pageSize=val;
                //查询总数
                this.selectHousefacilitiesdetailsCount();
                //执行查询设施服务类型
                this.selectHouseFacilitiesDetails();
            },
            handleCurrentChange(val) {
                console.log(`当前页: ${val}`);
                this.currentPage=val;
                console.log(this.pageSize);
                //查询总数
                this.selectHousefacilitiesdetailsCount();
                //执行查询设施服务类型
                this.selectHouseFacilitiesDetails();
            },
            //删除
            deletes:function () {
                this.$message("不允许删除哦！");
            },
            //编辑
            edit:function (index) {
                this.dialogFormVisible=true;
                this.hfdId=this.houseFacilitiesDatis[index].hfdId;
                this.hfdName=this.houseFacilitiesDatis[index].hfdName;
                this.hfValues=this.hfValue;
            },
            //修改
            update:function () {
                var that=this;
                var hfValues="";
                if(this.hfValues=="居家"){
                    hfValues=1;
                }else{
                    hfValues=this.hfValues;
                }
                var datas=qs.stringify({
                  hfdName:this.hfdName,
                  hfId:hfValues,
                    hfdId:this.hfdId
                });
                this.axios.put("http://localhost:8080/houseFacilitiesReflect/update_housefacilitiesdetails?"+datas).then(function (response) {
                    //查询总数
                    that.selectHousefacilitiesdetailsCount();
                    //执行查询设施服务类型
                    that.selectHouseFacilitiesDetails();
                    that.dialogFormVisible=false;
                    if(response.data.code==1){
                        that.$message("修改成功！");
                    }
                }).catch(function (error) {
                    console.log(error);
                })
            },
            //显示框
            showAdd:function () {
                this.dialogFormVisible=true;
            },
            //添加
            add:function () {
                var that=this;
                var hfValues="";
                if(this.hfValues=="居家"){
                    hfValues=1;
                }else{
                    hfValues=this.hfValues;
                }
                this.axios.post("http://localhost:8080/houseFacilitiesReflect/insert_housefacilitiesdetails",
                    qs.stringify({
                        hfId:hfValues,
                        hfdName:that.hfdName
                    })
                ).then(function (response) {
                    //查询总数
                    that.selectHousefacilitiesdetailsCount();
                    //执行查询设施服务类型
                    that.selectHouseFacilitiesDetails();
                    that.dialogFormVisible=false;
                    if(response.data.code==1){
                        that.$message("添加成功！");
                    }
                }).catch(function (error) {
                    console.log(error);
                })
            }
        },
        //观察属性
        watch:{
            hfValue:function(newValue,oldValue){
                this.hfValue=newValue;
                this.currentPage=1;//每次选择不同的值时，都变为第一页
                //查询总数
                this.selectHousefacilitiesdetailsCount();
                //执行查询设施服务类型
                this.selectHouseFacilitiesDetails();
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
