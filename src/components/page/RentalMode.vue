<template>
    <div class="RentalMode">
      <!--  <h3 style="font-family: 微软雅黑">出租方式管理</h3>-->
        <marquee style="color: cadetblue">出租方式管理</marquee>
        <el-table
                :data="bedData"
                style="width: 100%"
                :default-sort = "{prop: 'date', order: 'descending'}"
        >
            <el-table-column
                    prop="rmId"
                    label="编号"
                    sortable
                    width="350">
            </el-table-column>
            <el-table-column
                    prop="rmName"
                    label="类型"
                    sortable
                    width="350">
            </el-table-column>
            <el-table-column
                    fixed="right"
                    label="操作"
                    width="100">
                <template slot-scope="scope">
                    <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
                    <el-button type="text" size="small" @click="editFirstRows(scope.row)">编辑</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!--
            新增
        -->
        <el-button type="primary" @click="dialogFormVisible = true">新增类型</el-button>
        <!--
            弹出层
        -->
        <el-dialog title="信息操作" :visible.sync="dialogFormVisible">
            <el-form >
                <el-form-item label="编号" :label-width="formLabelWidth">
                   <!-- <el-input  v-model="this.rmId" autocomplete="off"></el-input>-->
                    <el-input v-model="form.rmId" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="类型" :label-width="formLabelWidth">
                    <el-input v-model="form.rmName" autocomplete="" placeholder="请输入内容"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="addOne()">新 增</el-button>
                <el-button type="primary" @click="editSucc()">编 辑</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
    import  qs from 'qs'
    export default {
        name: "RentalMode",
        methods: {
            handleClick(row) {
                console.log(row);
            },
            editFirstRows(row){
                this.dialogFormVisible=true;
                console.log(row);
                this.form.rmId=row.rmId;
                this.form.rmName=row.rmName;
            },

            /*
            * 编辑 put请求
            * */
            editSucc(){
                var that = this;

                var readyData= qs.stringify({
                    rmName: this.form.rmName,
                    rmId: this.form.rmId
                });
                this.axios.put("http://localhost:8080/houseBed/update_rentalMode?"+readyData).then(function (res) {
                   console.log(res);
                    if(res.data.code===1){
                        that.$message({
                            message: '成功',
                            type: 'success'
                        });
                        that.axios.get("http://localhost:8080/houseBed/select_rentalMode",{}).then(function (res) {
                            that.bedData=res.data.data;
                            console.log(that.bedData)
                        })
                    }
                });
                this.dialogFormVisible=false;
            },

            /*
            * 新增 post请求
            * */
            addOne(){
                var that = this;
                if(this.form.rmName==""){
                    this.$message({
                        message: '空值错误',
                        type: 'warning'
                    });
                    return false;
                }
                this.axios.post("http://localhost:8080/houseBed/insert_rentalMode",qs.stringify({
                    rmName: this.form.rmName
                })).then(function (res) {
                    console.log(res.data.code);
                    if(res.data.code==1){
                        that.$message({
                            message: '成功',
                            type: 'success'
                        });
                        that.axios.get("http://localhost:8080/houseBed/select_rentalMode",{}).then(function (res) {
                            that.bedData=res.data.data;
                            console.log(that.bedData)
                        })
                    }
                });

                this.dialogFormVisible=false;

            }
        },
        data() {
            return {
                bedData:[],
                tableData: [],
                dialogFormVisible: false,
                form: {
                    rmId:"",
                    rmName:""
                },
                formLabelWidth: '120px'
            };
        },
        created() {
            var that=this;
            /*
            * 跨域请求数据
            * */
            this.axios.get("http://localhost:8080/houseBed/select_rentalMode",{}).then(function (res) {
                //this.bedData=res.data.data;
                that.bedData=res.data.data;
                console.log(that.bedData)
            })
        }
    }
</script>

<style scoped>
    .content-box{
        background: none!important;
    }
</style>
