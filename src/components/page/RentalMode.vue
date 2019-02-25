<template>
    <div class="RentalMode">
      <!--  <h3 style="font-family: 微软雅黑">出租方式管理</h3>-->
        <marquee style="color: cadetblue">出租方式管理</marquee>
        <el-table
                :data="bedData"
                border
                style="width: 100%">
            <el-table-column
                    prop="rmId"
                    label="编号"
                    width="150">
            </el-table-column>
            <el-table-column
                    prop="rmName"
                    label="类型"
                    width="120">
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
           <!-- <el-table-column
                    prop="province"
                    label="省份"
                    width="120">
            </el-table-column>
            <el-table-column
                    prop="city"
                    label="市区"
                    width="120">
            </el-table-column>
            <el-table-column
                    prop="address"
                    label="地址"
                    width="300">
            </el-table-column>
            <el-table-column
                    prop="zip"
                    label="邮编"
                    width="120">
            </el-table-column>-->

        </el-table>
        <br/>
        <!--<h3 style="font-family: 微软雅黑">房屋类型管理</h3>
        <el-table
                :data="tableData"
                border
                style="width: 100%">
            <el-table-column
                    prop="date"
                    label="日期"
                    width="150">
            </el-table-column>
            <el-table-column
                    prop="name"
                    label="姓名"
                    width="120">
            </el-table-column>
            <el-table-column
                    prop="province"
                    label="省份"
                    width="120">
            </el-table-column>
            <el-table-column
                    prop="city"
                    label="市区"
                    width="120">
            </el-table-column>
            <el-table-column
                    prop="address"
                    label="地址"
                    width="300">
            </el-table-column>
            <el-table-column
                    prop="zip"
                    label="邮编"
                    width="120">
            </el-table-column>
            <el-table-column
                    fixed="right"
                    label="操作"
                    width="100">
                <template slot-scope="scope">
                    <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
                    <el-button type="text" size="small" @click="editRows(scope.row)">编辑</el-button>
                </template>
            </el-table-column>
        </el-table>-->
        <!--
            弹出层
        -->
        <el-dialog title="编辑信息" :visible.sync="dialogFormVisible">
            <el-form >
                <el-form-item label="编号" :label-width="formLabelWidth">
                   <!-- <el-input  v-model="this.rmId" autocomplete="off"></el-input>-->
                    <el-input v-model="form.rmId" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="类型" :label-width="formLabelWidth">
                    <el-input v-model="form.rmName" autocomplete=""></el-input>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="dialogFormVisible = false">取 消</el-button>
                <el-button type="primary" @click="editSucc()">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
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
            editSucc(){

                console.log(this.form.rmName);
                this.axios.put("http://localhost:8080/houseBed/update_rentalMode",{
                    params: {
                        hcName: this.form.rmName,
                        hcId: this.form.rmId
                    }
                }).then(function (res) {
                   console.log("返回值："+res);
                });
                this.dialogFormVisible = false;
                const h = this.$createElement;
                this.$notify({
                    title: '编辑成功',
                   // message: h('i', { style: 'color: teal'}, '这是提示文案这是提示文案这是提示文案这是提示文案这是提示文案这是提示文案这是提示文案这是提示文案')
                });
            }
        },
        data() {
            return {
                bedData:[],
                tableData: [{
                    date: '2016-05-02',
                    name: '王小虎',
                    address: '上海市普陀区金沙江路 1518 弄'
                }, {
                    date: '2016-05-04',
                    name: '王小虎',
                    address: '上海市普陀区金沙江路 1518 弄'
                }, {
                    date: '2016-05-01',
                    name: '王小虎',
                    address: '上海市普陀区金沙江路 1518 弄'
                }, {
                    date: '2016-05-03',
                    name: '王小虎',
                    address: '上海市普陀区金沙江路 1518 弄'
                }],
                dialogFormVisible: false,
                form: {
                    rmId:"",
                    rmName:"",
                   /* name: '',
                    region: '',
                    date1: '',
                    date2: '',
                    delivery: false,
                    type: [],
                    resource: '',
                    desc: ''*/
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
