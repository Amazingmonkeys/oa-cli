<template>
  <div class="login">
    <el-card class="login-card">
      <div
        slot="header"
        style="font-size: 20px; font-weight: bold; color: #666666"
      >
        <!--设置一个在卡片顶部的插槽-->
        OA云协作
      </div>
      <el-form
        ref="loginForm"
        :model="user"
        :rules="loginFormRules"
        style="margin-top: 20px"
        size="large"
      >
        <el-form-item style="height: 70px" prop="u_id">
          <el-input v-model="user.u_id" placeholder="账号"></el-input>
        </el-form-item>
        <el-form-item style="height: 70px" prop="u_pwd">
          <el-input
            v-model="user.u_pwd"
            :show-password="true"
            type="password"
            placeholder="密码"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="login" style="width: 100%"
            >登录</el-button
          >
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {}, //登录表单数据
      loginFormRules: {
        //定义验证规则
        u_id: [{ required: true, message: "请输入账号！", trigger: "blur" }],
        u_pwd: [{ required: true, message: "请输入密码！", trigger: "blur" }],
      },
    };
  },

  methods: {
    login() {
      this.$refs["loginForm"].validate((valid) => {
        if (valid) {
          //以form-data格式数据发送post请求
          this.$post("security/login", this.user).then(() => {
            //该函数在响应成功之后执行
            this.$router.push("/home");
          });
          console.log("OK");
        } else {
          console.log("err");
        }
        return false; //阻止表单事件的默认行为（提交）
      });
    },
  },
};
</script>

<style scoped>
.login {
  position: fixed; /*固定定位，相对于HTML*/
  left: 0px; /*元素的左边与窗口的最左侧距离为0 */
  right: 0px;
  top: 0px;
  bottom: 0px;

  display: flex; /*定义本元素内部的元素采用flex布局 */
  flex-direction: row; /*定义主轴方向，内部元素优先按按主轴排序，本例为行 */
  flex-wrap: wrap; /*主轴排列方向排不下则换行 */
  justify-content: center;
  align-items: center;
}

.login-card {
  width: 400px;
  margin-bottom: 100px;
}
</style>