<template>
    <div class="BedType">
        <marquee style="color: cadetblue">床型管理</marquee>
        <el-table
                :data="BedTypeData"
                style="width: 100%"
                :default-sort = "{prop: 'date', order: 'descending'}"
        >
            <el-table-column
                    prop="hbtId"
                    label="编号"
                    sortable
                    width="550">
            </el-table-column>
            <el-table-column
                    prop="hbtName"
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
                    <el-input id="TypeNo" v-model="form.htbId" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="类型" :label-width="formLabelWidth">
                    <el-input id="TypeVal" v-model="form.htbName" autocomplete="" placeholder="请输入内容"></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="addOne()">新 增</el-button>
                <el-button type="primary" @click="editSucc()">编 辑</el-button>
            </div>
        </el-dialog>
        <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage1"
                :page-sizes="[3,5,10]"
                :page-size="5"
                layout="total, sizes, prev, pager, next, jumper"
                :total="countPageData">
        </el-pagination>
    </div>
</template>

<script>
    import  qs from 'qs'
    export default {
        name: "BedType",
        methods: {
            handleSizeChange(val) {
                this.currentPageSize=val;
                this.norMalSelect();
            },
            handleCurrentChange(val) {
                this.currentPage1=val;
                this.norMalSelect();

            },
            /*
            * 封装查询
            * */
            norMalSelect(){
                var that=this;
                this.axios.get("http://localhost:8080/houseBed/select_houseBedType",{
                    params:{
                        no:that.currentPage1,
                        pagesize:that.currentPageSize
                    }
                }).then(function (res) {
                    that.BedTypeData=res.data.data;
                })
            },

            handleClick(row) {
                console.log(row);
            },

            editFirstRows(row){
                this.dialogFormVisible=true;
                this.form.htbId=row.hbtId;
                this.form.htbName=row.hbtName;
            },

            /*
            * 编辑 put 请求
            * */
            editSucc(){
                var that = this;
                var readyData= qs.stringify({
                    hbtName: this.form.htbName,
                    hbtId: this.form.htbId
                });
                this.axios.put("http://localhost:8080/houseBed/update_houseBedType?"+readyData).then(function (res) {
                    if(res.data.code===1){
                        that.$message({
                            message: '成功',
                            type: 'success'
                        });
                        that.norMalSelect();
                    }
                });
                this.dialogFormVisible=false;
            },

            /*
            * 新增 post请求
            * */
            addOne(){
                var that = this;
                if(this.form.htbame===""){
                    this.$message({
                        message: '空值错误',
                        type: 'warning'
                    });
                    return false;
                }
                console.log(this.form.htbName);
                this.axios.post("http://localhost:8080/houseBed/insert_houseBedType",qs.stringify({
                    hbtName: this.form.htbName
                })).then(function (res) {
                    console.log(res.data.code);
                    if(res.data.code==1){
                        that.$message({
                            message: '成功',
                            type: 'success'
                        });
                        //查询总数
                        that.axios.get("http://localhost:8080/houseBed/select_houseBedTypeCount",
                        ).then(function (res) {
                            that.countPageData=res.data;
                        });
                        that.norMalSelect();
                    }
                });
                this.dialogFormVisible=false;

            }
        },
        data() {
            return {
                //分页
                currentPage1: 1,
                currentPageSize:5,
                countPageData:0,
                BedTypeData:[],
                dialogFormVisible: false,
                form: {
                    htbId:"",
                    htbName:""
                },
                formLabelWidth: '120px'
            };
        },
        beforeMount() {
            var that=this;

            //查询总数
            this.axios.get("http://localhost:8080/houseBed/select_houseBedTypeCount",
            ).then(function (res) {
                that.countPageData=res.data;
            });
            //默认加载
            this.norMalSelect();
        }
    }
</script>

<style scoped>

</style>
