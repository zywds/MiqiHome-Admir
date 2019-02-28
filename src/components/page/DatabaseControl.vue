<template>
    <div>
        <marquee style="color: cadetblue">每天自动备份的时间为3：00</marquee>
        <el-button type="info" style="margin-bottom: 10px;margin-right: 10px">查询备份情况</el-button>
        <el-button type="info" style="margin-bottom: 10px;margin-right: 10px" @click="insertDatabaseBackup()">立即备份</el-button>
        <table>
            <tr>
                <th>编号</th>
                <th>备份路径</th>
                <th>备份时间</th>
                <th>还原时间</th>
                <th>操作</th>
            </tr>
            <tr v-for="item in databaseBackup">
                <td>{{item.dpId}}</td>
                <td>{{item.dpPath}}</td>
                <td>{{item.dpBTime|timeFilter}}</td>
                <td>{{item.dpFTime|timeFilter}}</td>
                <td>
                    <el-button type="primary" icon="el-icon-edit" circle></el-button>
                    <el-button type="danger" icon="el-icon-delete" circle @click="deleteDatabaseBackup(item.dpId)"></el-button>
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
    </div>
</template>

<script>
    import qs from 'qs'
    export default {
        name: "DatabaseControl",
        data(){
            return{
                databaseBackup:[],
                currentPage:1,
                pageSize:2,
                total:100
            }
        },
        //挂载后
        mounted:function(){
            //查询所有备份记录
            this.selectDatabaseBackup();
        },
        //过滤器
        filters:{
          timeFilter:function (value) {
              return new Date(value).toLocaleString();
          }
        },
        //计算属性
        computed:{

        },
        methods:{
            //查询所有备份记录
            selectDatabaseBackup:function () {
                var that=this;
                this.axios.get("http://localhost:8080/databaseBackup/select_databaseBackup_count").then(function (response) {
                    that.total=response.data.data;
                    that.axios.get("http://localhost:8080/databaseBackup/select_databaseBackup",{
                        params:{
                            page:that.currentPage,
                            limit:that.pageSize
                        }
                    }).then(function (response) {
                        that.databaseBackup=response.data.data;
                    }).catch(function (error) {
                        console.log(error);
                    });
                }).catch(function (error) {
                    console.log(error);
                });
            },
            //每页数量改变
            handleSizeChange:function (val) {
                this.currentPage=1;
                //获得每页数量
                this.pageSize=val;
                this.selectDatabaseBackup();
            },
            //页数改变
            handleCurrentChange:function (val) {
                this.currentPage=1;
                //获得当前页数
                this.currentPage=val;
                this.selectDatabaseBackup();
            },
            //立即备份
            insertDatabaseBackup:function () {
                var that=this;
                this.axios.post("http://localhost:8080/databaseBackup/insert_databaseBackup").then(function (response) {
                    if(response.data.code==1){
                        that.$message("备份成功！");
                        that.selectDatabaseBackup();
                    }
                }).catch(function (error) {
                    console.log(error);
                })
            },
            //删除备份
            deleteDatabaseBackup:function (dpId) {
                var that=this;
                this.$confirm('将删除数据库备份, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.axios.delete("http://localhost:8080/databaseBackup/delete_databaseBackup?dpId="+dpId).then(function (response) {
                        if(response.data.code==1){
                            that.$message("删除成功！");
                            that.selectDatabaseBackup();
                        }
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取删除'
                    });
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
