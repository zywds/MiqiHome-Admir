<template>
    <div class="Logger">

    </div>
</template>

<script>
    import $ from 'jquery'

    export default {
        name: "Logger",
        data(){
            return{
                longLogMsg:"",
              /*  bigArr:[],
                smallArr:[]*/
            }
        },
        created() {
            var c=0;
            this.axios.post("http://localhost:8080/logController/sendLog",{}).then((res)=>{
                this.longLogMsg=res.data.msg;
               /* this.bigArr=this.longLogMsg.split(';');
                console.log(this.bigArr);*/
               $(".Logger").html(res.data.msg)
            })
        }
    }
</script>

<style scoped lang="less">
    .Logger{
        overflow: auto;
        background: #000000c7;
        color: white;
        width: 100%;
        height: 100%;
        padding-left: 10px;
    }

</style>
