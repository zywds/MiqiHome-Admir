<template>
    <div class="login-wrap">
        <div class="ms-login">
            <div class="ms-title">妙妙屋后台管理系统</div>
            <el-form label-width="0px" class="ms-content">
                <el-form-item prop="username">
                    <el-input v-model="username" placeholder="username">
                        <el-button slot="prepend" icon="el-icon-lx-people"></el-button>
                    </el-input>
                </el-form-item>
                <el-form-item prop="password">
                    <!--按下enter键自动登录-->
                    <el-input type="password" placeholder="password" v-model="password" @keyup.enter.native="login()">
                        <el-button slot="prepend" icon="el-icon-lx-lock"></el-button>
                    </el-input>
                </el-form-item>
                <div class="login-btn">
                    <el-button type="primary" @click="login()">登录</el-button>
                </div>
                <p class="login-tips">Tips : 请填写正确的用户名密码。</p>
            </el-form>
        </div>
    </div>
</template>

<script>

    export default {
        data: function(){
            return {
                username: '',
                password: ''
            }
        },
        //过滤器
        filters:{

        },
        //监听器
        watch:{
            username:function (newValue) {
                if(!newValue){
                    //为空的操作
                    this.$message({
                        message:"用户名不可为空",
                        type: 'warning'
                    });
                }
            },
            password:function (newValue) {
                if(!newValue){
                    //为空的操作
                    this.$message({
                        message:"密码不可为空！",
                        type: 'warning'
                    });
                }
            }
        },
        methods: {
            login:function () {
                var that=this;
                if(!this.username){
                    this.$message({
                        message:"用户名不可为空！",
                        type: 'warning'
                    });
                    return false;
                }if(!this.password){
                    this.$message({
                        message:"密码不可为空！",
                        type: 'warning'
                    });
                    return false;
                }if(!this.username && !this.password){
                    this.$message({
                        message:"用户名与密码不可为空！",
                        type: 'warning'
                    });return false;
                }
                //验证账号密码是否正确
                //axios跨域请求
                this.axios.get("http://localhost:8080/regsistAdmin/login_regsistAdmin",{
                    params:{
                        raPhone:this.username,
                        raPassword:this.password
                    }
                }).then(function (response) {
                    //console.log(response.data);
                    if(response.data.code==1){
                        that.$message({
                            message:"登录成功！正在跳转。。",
                            type: 'success'
                        });
                        setTimeout(function () {
                            localStorage.setItem('ms_username',that.username);
                            that.$router.push('/');
                        },1000)
                    }else{
                        that.$message({
                            message:"登录失败！请重新登录。。",
                            type: 'error'
                        });
                    }
                }).catch(function (error) {
                    console.log(error);
                    console.log("");
                })
            }
        }
    }
</script>

<style scoped>
    .login-wrap{
        position: relative;
        width:100%;
        height:100%;
        background-image: url(../../assets/img/login-bg2.png);
        background-size: 100%;
    }
    .ms-title{
        width:100%;
        line-height: 50px;
        text-align: center;
        font-size:20px;
        color: #fff;
        border-bottom: 1px solid #ddd;
    }
    .ms-login{
        position: absolute;
        left:50%;
        top:50%;
        width:350px;
        margin:-190px 0 0 -175px;
        border-radius: 5px;
        background: rgba(255,255,255, 0.3);
        overflow: hidden;
    }
    .ms-content{
        padding: 30px 30px;
    }
    .login-btn{
        text-align: center;
    }
    .login-btn button{
        width:100%;
        height:36px;
        margin-bottom: 10px;
    }
    .login-tips{
        font-size:12px;
        line-height:30px;
        color:#fff;
    }
</style>
