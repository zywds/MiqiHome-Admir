<template>
    <div class="LodgerRule">
        <marquee style="color: cadetblue">房客要求管理</marquee>
        <el-table
                :data="LodgerRuleData"
                style="width: 100%"
                :default-sort = "{prop: 'date', order: 'descending'}"
        >
            <el-table-column
                    prop="hreId"
                    label="编号"
                    sortable
                    width="550">
            </el-table-column>
            <el-table-column
                    prop="hreName"
                    label="权限"
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
        <el-button type="primary" @click="dialogFormVisible = true">新增数据</el-button>
        <!--
            弹出层
        -->
        <el-dialog title="信息操作" :visible.sync="dialogFormVisible">
            <el-form >
                <el-form-item label="编号" :label-width="formLabelWidth">
                    <el-input :disabled="true" v-model="form.hreId" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="权限" :label-width="formLabelWidth">
                    <el-input v-model="form.hreName" autocomplete="" placeholder="请输入内容"></el-input>
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
                :total="allCount">
        </el-pagination>
    </div>
</template>

<script>
    import  qs from 'qs'
    export default {
        name: "LodgerRule",
        methods: {
            changeVal(){
                console.log(this.checkVal)
            },
            handleSizeChange(val) {
                this.currentPageSize=val;
                this.norMalSelect();
            },
            handleCurrentChange(val) {
                this.currentPage1=val;
                this.norMalSelect();
            },
            editFirstRows(row){
                this.dialogFormVisible=true;
                console.log(row);
                this.form.hreId=row.hreId;
                this.form.hreName=row.hreName;
            },
            /*
            * 封装查询
            * */
            norMalSelect(){
                var that=this;
                this.axios.post("http://localhost:8080/HomeRequirement/select_Homerequirement_byLimit",qs.stringify({
                        no:that.currentPage1,
                        pagesize:that.currentPageSize
                })).then(function (res) {
                    that.LodgerRuleData=res.data;
                })
            },

            /*
            * 编辑 post 请求
            * */
            editSucc(){
                var that = this;
                if(that.form.hreName===""){
                    that.$message({
                        message: '空值错误',
                        type: 'warning'
                    });
                    return false;
                }
                this.axios.post("http://localhost:8080/HomeRequirement/update_Homerequirement",{
                    hreId:that.form.hreId,
                    hreName:that.form.hreName
                }).then((res)=>{
                    if(res.data>0){
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
                if(that.form.hreName===""){
                    that.$message({
                        message: '空值错误',
                        type: 'warning'
                    });
                    return false;
                }

                //console.log(that.form.hreName)
                this.axios.post("http://localhost:8080/HomeRequirement/insert_Homerequirement",{
                    hreName:that.form.hreName
                }).then((res)=>{
                    if(res.data>0){
                        that.$message({
                            message: '成功',
                            type: 'success'
                        });
                        that.axios.get("http://localhost:8080/HomeRequirement/select_Homerequirement_count",{}).then((res)=>{
                            that.allCount=res.data;
                        });
                        //查询参数数据
                        that.norMalSelect();
                    }else {
                        that.$message({
                            message: '失败',
                            type: 'warning'
                        });
                    }
                });
                this.dialogFormVisible=false;
            },
        },
        data() {
            return {
                allCount:0,
                currentPage1: 1,
                currentPageSize:5,

                LodgerRuleData:[],
                dialogFormVisible: false,
                form: {
                    hreId:"",
                    hreName:""
                },
                formLabelWidth: '120px',
            };
        },

        /*
        * 创建初 查询
        * */
        created() {
            var that=this;
            //查询参数总数
            this.axios.get("http://localhost:8080/HomeRequirement/select_Homerequirement_count",{}).then((res)=>{
                that.allCount=res.data;
            });
            //查询参数数据
            this.norMalSelect();
        }
    }
</script>

<style scoped>

</style>
