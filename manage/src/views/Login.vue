<template>
  <div class="register">
    <div class="register-box clearfix">
      <div class="manage-title">商家后台管理系统</div>
      <div class="form-box">
        <div class="form-group clearfix">
          <label class="fl form-title" for="email">邮箱</label>
          <div class="fl form-input">
            <input
              type="text"
              class="form-control"
              id="email"
              autocomplete="off"
              v-model="userInfo.email"
              placeholder="请输入邮箱"
            />
          </div>
        </div>

        <div class="form-group clearfix">
          <label class="fl form-title" for="password">密码</label>
          <div class="fl form-input">
            <input
              type="password"
              class="form-control"
              id="password"
              autocomplete="off"
              v-model="userInfo.password"
              placeholder="密码6-16个字符"
            />
          </div>
        </div>

        <div class="form-group form-btn-box">
          <div class="btn-box">
            <button class="btn btn-primary btn-block" @click="login">登录</button>
          </div>

          <div class="clearfix login-text">
            <span class="fl" @click="goRegister">没有账号，立即注册</span>
            <span class="fr">找回密码</span>
          </div>
        </div>
      </div>
    </div>

    <!-- 消息提示 -->
    <MsgBox></MsgBox>
  </div>
</template>

<script>
//导入表单验证文件
import { validForm } from "../assets/js/validForm";

//导入消息提示组件
import MsgBox from "../components/MsgBox";

export default {
  name: "Register",
  data() {
    return {
      userInfo: {
        email: "",
        password: ""
      }
    };
  },

  methods: {
    login() {
      //表单验证
      let result = validForm.valid(this.userInfo);
      if (result.pass === false) {
        this.$showToast({
          message: result.msg,
          duration: 3000
        });
        return;
      }

      //发起登录请求
      this.axios({
        method: "POST",
        url: "/login",
        data: this.userInfo
      })
        .then(result => {
          console.log("result ==>", result);
          
          this.$showToast({
            message: result.data.msg,
            duration: 3000
          })
          if (result.data.code == 1020) {
            this.$cookies.set("_abc", result.data.token, "5d");
            this.$router.push({ name: "Echarts" });
          }
          
        })
        .catch(err => {
          console.log("err ==> ", err);
        });
    },

    //跳转到注册
    goRegister() {
      this.$router.push({ name: "Register" });
    }
  },

  //注册局部组件  vue自带组件
  components: {
    MsgBox
  }
};
</script>

<style lang="less" scoped>
.login-text {
  margin-top: 20px;
  color: #444;
  cursor: pointer;
  margin-left: 80px;
}
.register-box {
  width: 1170px;
  margin:0 auto;
}
.manage-title {
  font-size: 48px;
  color: #fff;
  width: 400px;
  text-align: center;
  margin: 0 auto;
  line-height: 200px;
}
.form-box{
  margin: 0 auto;
}
.register {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background: url("../assets/images/bg.jpg") no-repeat center center;
  background-size: cover;
  min-width: 1170px;
   
}
.valid-code {
  width: 140px;
}

.form-btn-box {
  margin-top: 40px;
  margin-bottom: 0;
}
.btn-box {
  margin-left: 80px;
}
.mask {
  position: absolute;
  left: 0;
  top: 0;
  width: 60px;
  height: 100%;
  background-color: #e4393c;
  cursor: pointer;
}
.valid-box {
  position: relative;
  height: 38px;
  margin-left: 80px;
  background-color: #ddd;
  text-align: center;
  line-height: 38px;
  user-select: none;
  span {
    color: #fff;
  }
}
.form-box {
  width: 440px;
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
}

.form-title {
  display: block;
  width: 80px;
  height: 38px;
  line-height: 38px;
}

.form-input {
  width: 320px;
}

.valid-form-input {
  width: 160px;
  margin-right: 20px;
}
</style>