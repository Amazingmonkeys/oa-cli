<template>
    <div class="my-container">
       <div style="height:50%;width:50%;font-size:50px;line-height:50%;text-align:center">
           <i class="el-icon-magic-stick"></i> 索尔科技欢迎您！<span class="fa fa-handshake-o"></span> 
        </div> 
    </div>
</template>

<script>
export default {
    //本组件渲染完成，执行该方法
    mounted(){
        //setTimeout函数中有两个参数，第一个参数是一个函数，第二个参数是毫秒数
        //该函数在第二个参数指定的毫秒数后执行第一个参数的函数
        setTimeout(()=>{
            //跳转到login.vue
            this.$router.push('/login');
        },2000);
    }
}
</script>

<style scoped>

.my-container{
    position:fixed;
    top:0px;
    bottom:0px;
    left:0px;
    
    width:100%;  
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;

}

</style>