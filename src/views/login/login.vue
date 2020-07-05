<template>
  <div>
    <img src="../../assets/logo.png">
    <div class="login-wrap" v-show="showLogin">
      <h3>登录</h3>
      <p v-show="showTishi">{{tishi}}</p>
      <input class="inp" type="text" placeholder="请输入用户名" v-model="username">
      <input class="inp" type="password" placeholder="请输入密码" v-model="password">
      <button v-on:click="login">登录</button>
      <span v-on:click="ToRegister">没有账号？马上注册</span>
    </div>

    <div class="register-wrap" v-show="showRegister">
      <h3>注册</h3>
      <p v-show="showTishi">{{tishi}}</p>
      <input class="inp" type="text" placeholder="请输入用户名" v-model="newUsername">
      <input class="inp" type="password" placeholder="请输入密码" v-model="newPassword">
      <button v-on:click="register">注册</button>
      <span v-on:click="ToLogin">已有账号？马上登录</span>
    </div>
  </div>
</template>

<style>
  .login-wrap {
    text-align: center;
  }

  .register-wrap {
    text-align: center;
  }

  .inp {
    display: block;
    width: 250px;
    height: 40px;
    line-height: 40px;
    margin: 0 auto;
    margin-bottom: 10px;
    outline: none;
    border: 1px solid #888;
    padding: 10px;
    box-sizing: border-box;
  }

  p {
    color: red;
  }

  button {
    display: block;
    width: 250px;
    height: 40px;
    line-height: 40px;
    margin: 0 auto;
    border: none;
    background-color: #41b883;
    color: #fff;
    font-size: 16px;
    margin-bottom: 5px;
  }

  span {
    cursor: pointer;
  }

  span:hover {
    color: #41b883;
  }

  #app {
    text-align: center;
    color: #2c3e50;
    margin-top: 80px;
  }
</style>

<script>
  import {setCookie,getCookie} from '../../assets/js/cookie.js'
  export default{
    data(){
      return {
        showLogin: true,
        showRegister: false,
        showTishi: false,
        tishi: '',
        username: '',
        password: '',
        newUsername: '',
        newPassword: ''
      }
    },
    mounted(){
      /*页面挂载获取cookie，如果存在username的cookie，则跳转到主页，不需登录*/
      if (getCookie('username')) {
        this.$router.push('/home')
      }
    },
    methods: {
      login(){
        if (this.username == "" || this.password == "") {
          alert("请输入用户名或密码")
        } else {
          this.password = this.$md5(this.password)
          let data = {
            'userName': this.username, 
            'password': this.password,
            }
          /*接口请求*/
          this.$http.post('/asyysy/api/user/login', data).then((res) => {
            console.log(res.bodyText)
          /*接口的传值是(-1,该用户不存在),(0,密码错误)，同时还会检测管理员账号的值*/
          if (res.body.type == "FAIL") {
            this.tishi = res.body.msg
            this.showTishi = true
          // else if(res.data == 'admin'){
          //       /*路由跳转this.$router.push*/
          //       this.$router.push('/main')
          //     }
          } else {
            this.tishi = "登录成功"
            this.showTishi = true
            setCookie('username', this.username, 1000 * 60)
            setTimeout(function () {
              this.$router.push('/home')
            }.bind(this), 1000)
          }
        })
        }
      },
      ToRegister(){
        this.showRegister = true
        this.showLogin = false
      },
      ToLogin(){
        this.showRegister = false
        this.showLogin = true
      },
      register(){
        if (this.newUsername == "" || this.newPassword == "") {
          alert("请输入用户名或密码")
        } else {
          let data = {'username': this.newUsername, 'password': this.newPassword}
          this.$http.post('http://localhost/vueapi/index.php/Home/user/register', data).then((res) => {
            console.log(res)
          // if(res.data == "ok"){
          //   this.tishi = "注册成功"
          //   this.showTishi = true
          //   this.username = ''
          //   this.password = ''
          //   /*注册成功之后再跳回登录页*/
          //   setTimeout(function(){
          //     this.showRegister = false
          //     this.showLogin = true
          //     this.showTishi = false
          //   }.bind(this),1000)
          // }
        })
        }
      }
    }
  }
</script>
