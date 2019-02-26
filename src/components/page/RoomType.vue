<template>
    <div class="RoomType">
        <marquee style="color: cadetblue">房屋类型管理</marquee>
        <el-table
                :data="RoomTypeData"
                style="width: 100%"
                :default-sort = "{prop: 'date', order: 'descending'}"
        >
            <el-table-column
                    prop="htId"
                    label="编号"
                    sortable
                    width="550">
            </el-table-column>
            <el-table-column
                    prop="htName"
                    label="类型"
                    sortable
                    width="550">
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
                    <el-input id="TypeNo" v-model="form.htId" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="类型" :label-width="formLabelWidth">
                    <el-input id="TypeVal" v-model="form.htdName" autocomplete="" placeholder="请输入内容"></el-input>
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
        name: "RoomType",
        methods: {
            handleClick(row) {
                console.log(row);
            },
            editFirstRows(row){
                this.dialogFormVisible=true;
                this.form.htId=row.htId;
                this.form.htdName=row.htName;
            },

            /*
            * 编辑 post 请求
            * */
            editSucc(){
                var that = this;
                var readyData= qs.stringify({
                    htName: this.form.htdName,
                    htId: this.form.htId
                });
                this.axios.post("http://localhost:8080/houseBed/update_houseType",readyData).then(function (res) {
                    console.log(res);
                    if(res.data.code===1){
                        that.$message({
                            message: '成功',
                            type: 'success'
                        });
                        that.axios.get("http://localhost:8080/houseBed/select_houseType",{}).then(function (res) {
                            that.RoomTypeData=res.data.data;
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
                if(this.form.htdName==""){
                    this.$message({
                        message: '空值错误',
                        type: 'warning'
                    });
                    return false;
                }
                this.axios.post("http://localhost:8080/houseBed/insert_houseType",qs.stringify({
                    htName: this.form.htdName
                })).then(function (res) {
                    console.log(res.data.code);
                    if(res.data.code==1){
                        that.$message({
                            message: '成功',
                            type: 'success'
                        });
                        that.axios.get("http://localhost:8080/houseBed/select_houseType",{}).then(function (res) {
                            that.RoomTypeData=res.data.data;
                            console.log(that.RoomTypeData)
                        })
                    }
                });

                this.dialogFormVisible=false;

            }
        },
        data() {
            return {
                RoomTypeData:[],
                tableData: [],
                dialogFormVisible: false,
                form: {
                    htId:"",
                    htdName:""
                },
                formLabelWidth: '120px'
            };
        },
        created() {
            var that=this;
            /*
            * 跨域请求数据
            * */
            this.axios.get("http://localhost:8080/houseBed/select_houseType",{}).then(function (res) {
                //this.bedData=res.data.data;
                that.RoomTypeData=res.data.data;
            })
        }
    }
</script>

<style scoped>

</style>
