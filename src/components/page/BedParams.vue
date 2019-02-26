<template>
    <div class="BedParams">
        <!--  <h3 style="font-family: 微软雅黑">出租方式管理</h3>-->
        <marquee style="color: cadetblue">床型参数管理</marquee>
        <el-table
                :data="bedParamsData"
                style="width: 100%"
                :default-sort = "{prop: 'date', order: 'descending'}"
        >
            <el-table-column
                    prop="hdsId"
                    label="编号"
                    sortable
                    width="370">
            </el-table-column>
            <el-table-column
                    prop="hbsSize"
                    label="尺寸"
                    sortable
                    width="370">
            </el-table-column>
            <el-table-column
                    prop="housebedtype.hbtName"
                    label="类型"
                    sortable
                    width="370">
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
                <el-form-item label="类型" :label-width="formLabelWidth">
                    <el-select v-model="checkVal" placeholder="请选择" @change="changeVal()">
                        <el-option
                                v-for="item in options"
                                :key="item.hbtId"
                                :label="item.hbtName"
                                :value="item.hbtId">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="编号" :label-width="formLabelWidth">
                    <el-input :disabled="true" v-model="form.hdsId" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="尺寸" :label-width="formLabelWidth">
                    <el-input v-model="form.hbsSize" autocomplete="" placeholder="请输入内容"></el-input>
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
        name: "BedParams",
        methods: {
            test(){
                var that=this;
                let regsistadminList = [];
                regsistadminList.push("");
                regsistadminList.push("");
                regsistadminList.push("");
                regsistadminList.push("");
                regsistadminList.push("");
                regsistadminList.push(0);
                regsistadminList.push(10);

                //axios请求
              /*  this.axios({
                    method:"post",
                    url:"http://localhost:8080/regsistAdmin/select_regsistAdmin",
                    data:regsistadminList
                }).then((res)=>{
                    console.log(res.data)
                });*/
                this.axios.post("http://localhost:8080/regsistAdmin/select_regsistAdmin",regsistadminList).then((res)=>{
                    console.log(res.data);
                })
            }
            ,
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
                this.form.hdsId=row.hdsId;
                this.form.hbsSize=row.hbsSize;
                //this.checkVal=row.housebedtype.hbtName;
            },
            /*
            * 封装查询
            * */
            norMalSelect(){
                var that=this;
                this.axios.get("http://localhost:8080/houseBed/select_houseBedSize",{
                    params:{
                        no:that.currentPage1,
                        pagesize:that.currentPageSize
                    }
                }).then(function (res) {
                    that.bedParamsData=res.data.data;
                })
            },

            /*
            * 编辑 put请求
            * */
            editSucc(){
                var that = this;
                if(that.form.hbsSize===""||that.checkVal===""){
                    that.$message({
                        message: '空值错误',
                        type: 'warning'
                    });
                    return false;
                }


                var readyData= qs.stringify({
                    hbtId:that.checkVal,
                    hbsSize:that.form.hbsSize,
                    hdsId:that.form.hdsId
                });
                console.log(readyData);

                this.axios.put("http://localhost:8080/houseBed/update_houseBedSize?"+readyData).then((res)=>{
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
                if(that.form.hbsSize===""||that.checkVal===""){
                    that.$message({
                        message: '空值错误',
                        type: 'warning'
                    });
                    return false;
                }
                this.axios.post("http://localhost:8080/houseBed/insert_houseBedSize",qs.stringify({
                    hbtId:that.checkVal,
                    hbsSize:that.form.hbsSize
                })).then((res)=>{
                   if(res.data.code>0){
                       that.$message({
                           message: '成功',
                           type: 'success'
                       });
                       that.axios.get("http://localhost:8080/houseBed/select_houseBedSize_count",{}).then((res)=>{
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

                bedParamsData:[],
                dialogFormVisible: false,
                form: {
                    hdsId:"",
                    hbsSize:"",
                    housebedtype:"",
                    hbtName:""
                },
                formLabelWidth: '120px',

                options: [],
                checkVal:"",
            };
        },

        /*
        * 创建初 查询
        * */
        created() {
            var that=this;
            //查询参数总数
            this.$http.get("http://localhost:8080/houseBed/select_houseBedSize_count",{}).then((res)=>{
                that.allCount=res.data;
            });

            //查询全部类型
            this.axios.get("http://localhost:8080/houseBed/select_houseBedType",{
                params:{
                    no:1,
                    pagesize:100
                }
            }).then((res)=>{
                that.options=res.data.data;
            });

            //查询参数数据
           this.norMalSelect();

           this.test();
        }
    }
</script>

<style scoped>

</style>
