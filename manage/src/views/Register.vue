<template>
  <div class="register">

    <div class="register-box clearfix">
      <div class="manage-title fl">商家后台管理系统</div>
      
      <div class="form-box fr">

        <div class="form-group clearfix">
          <label class="fl form-title" for="email">邮箱</label>
          <div class="fl form-input">
            <input type="text" class="form-control" id="email" autocomplete="off" v-model="userInfo.email" placeholder="请输入邮箱">
          </div>
        </div>

        <div class="form-group clearfix">
          <label class="fl form-title" for="nickname">昵称</label>
          <div class="fl form-input">
            <input type="text" class="form-control" id="nickname" autocomplete="off" v-model="userInfo.nickname" placeholder="昵称1-10个字符">
          </div>
        </div>

        <div class="form-group clearfix">
          <label class="fl form-title" for="password">密码</label>
          <div class="fl form-input">
            <input type="text" class="form-control" id="password" autocomplete="off" v-model="userInfo.password" placeholder="密码6-16个字符">
          </div>
        </div>

        <div class="form-group clearfix">
          <label class="fl form-title" for="validcode">验证码</label>
          <div class="fl form-input valid-form-input">
            <input type="text" class="form-control" id="validcode" autocomplete="off" v-model="userInfo.code" placeholder="请输入六位验证码">
          </div>
          <div class="valid-code fl">
            <button class="btn btn-secondary btn-block" :disabled="isSend" @click="getCode">{{text}}</button>
          </div>
        </div>

        <!-- <div class="form-group clearfix">
          <div class="valid-box">
            <span>将滑块拖动到最右边</span>
            <div class="mask"></div>
          </div>
        </div> -->

        <div class="form-group form-btn-box">
          <div class="btn-box">
            <button class="btn btn-primary btn-block" @click="register">注册</button>
          </div>
          <div class="clearfix login-text">
            <span class="fr" @click="goLogin">已有账号，立即登录</span>
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
  import {validForm} from '../assets/js/validForm'

  //导入消息提示组件
  import MsgBox from '../components/MsgBox'

  export default {
    name: 'Register',
    data() {
      return {
        userInfo: {
          email: '',
          nickname: '',
          password: '',
          code: ''
        },

        text: '发送邮箱验证码',
        isSend: false
      };
    },

    methods: {

      //获取邮箱验证码
      getCode() {

        //验证邮箱格式是否正确
        let data = {email: this.userInfo.email};
        let res = validForm.valid(data);
        if (res.pass === false) {
          this.$showToast({
            message: res.msg,
            duration: 3000
          });
          
          return;
        }

        let time = 5;
        this.text = `${time}s后重新发送`;
        this.isSend = true;
        let timer = setInterval(() => {
          if (time == 0) {
            clearInterval(timer);
            timer = null;
            this.text = `发送邮箱验证码`;
            this.isSend = false;
          } else {
            time--;
            this.text = `${time}s后重新发送`;
          }
        }, 1000)

        //发送邮箱验证码
        this.axios({
          method: 'POST',
          url: '/sendmail',
          data,
        }).then(result => {
          console.log('result ==> ', result);
        }).catch(err => {
          console.log('err ==> ', err);
        })
      },

      //注册
      register() {

        //表单验证
        let result = validForm.valid(this.userInfo);
        if (result.pass === false) {
          
          this.$showToast({
            message: result.msg,
            duration: 3000
          });
          
          return;
        }

        //发起注册请求
        this.axios({
          method: 'POST',
          url: '/register',
          //post请求参数
          data: this.userInfo,
        }).then(result => {
          console.log('result ==> ', result);
          if (result.data.code == 1000) {
            this.goLogin();
          } else {
            this.$showToast({
              message: result.data.msg
            })
          }
        }).catch(err => {
          console.log('register 出错了 err ==> ', err);
        })
        
      },

      //跳转登录
      goLogin() {
        this.$router.push({name: 'Login'});
      }
    },

    components: {
      MsgBox
    }
  }
</script>

<style lang="less" scoped>
  .login-text{
    margin-top: 20px;
    color: #444;
    cursor: pointer;
  }
  .register-box{
    width: 1170px;
    margin: 100px auto;
  }
  .manage-title{
    font-size: 48px;
    color: #fff;
    width: 400px;
    text-align: center;
    line-height: 350px;
  }
  .register{
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background: url("../assets/images/bg.jpg") no-repeat center center;
    background-size: cover;
    min-width: 1170px;
  }
  .valid-code{
    width: 140px;
  }

  .form-btn-box{
    margin-top: 40px;
    margin-bottom: 0;
  }
  .btn-box{
    margin-left: 80px;
  }
  .mask{
    position: absolute;
    left: 0;
    top: 0;
    width: 60px;
    height: 100%;
    background-color: #e4393c;
    cursor: pointer;
  }
  .valid-box{
    position: relative;
    height: 38px;
    margin-left: 80px;
    background-color: #ddd;
    text-align: center;
    line-height: 38px;
    user-select: none;
    span{
      color: #fff;
    }
  }
  .form-box{
    width: 440px;
    background-color: #fff;
    padding: 20px;
    border-radius: 10px;
  }

  .form-title{
    display: block;
    width: 80px;
    height: 38px;
    line-height: 38px;
  }

  .form-input{
    width: 320px;
  }

  .valid-form-input{
    width: 160px;
    margin-right: 20px;
  }
</style>