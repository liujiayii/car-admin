
<template>
  <div id="login">
    <h3 class="loginTitle">Element-Admin后台管理模板</h3>
    <div class="loginBox">
      <el-form :model="loginForm" :rules="rules" ref="ruleForm">
        <el-form-item prop="username">
          <el-input placeholder="请输入账号" v-model="loginForm.username">
            <span slot="prepend" class="ico"><i class="fa fa-user fa-lg" /></span>
          </el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input placeholder="请输入密码" type="password" v-model="loginForm.password">
            <span slot="prepend" class="ico"><i class="fa fa-unlock-alt fa-lg" /></span>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-button class="sub" type="primary" :loading="loading" @click="submitForm('ruleForm')">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  import menus from '../../router.js'

  export default {
    name: "Login",
    data() {
      return {
        loginForm: {
          username: 'superAdmin',
          password: '123456'
        },
        rules: {
          username: [
            {required: true, message: '请输入用户名', trigger: 'blur'},
            {min: 3, max: 12, message: '长度在 3 到 12 个字符', trigger: 'blur'}
          ],
          password: [
            {required: true, message: '请输入密码', trigger: 'blur'},
            {min: 6, max: 12, message: '长度在 6 到 12 个字符', trigger: 'blur'}
          ]
        },
        loading: false
      };
    },
    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            this.loading = true
            let url = ''
            if (this.loginForm.username == 'admin') {
              url = './mock/admin.json'
            } else if (this.loginForm.username == 'superAdmin') {
              url = './mock/superAdmin.json'
            } else {
              url = './mock/user.json'
            }
            this.axios.get(url).then((res) => {
              console.log(res)
              if (this.loginForm.username == 'admin' || this.loginForm.username == 'superAdmin') {
                if(this.loginForm.username == res.data.username && this.loginForm.password == res.data.password){
                  this.$store.state.menu = res.data.userInfo.menu
                  this.$router.push({path: '/home'})
                  console.log(menus)
                 // this.$router.addRoutes(res.data.userInfo.menu)
                }else {
                  this.loading = false
                  this.$notify.error({
                    title: '错误',
                    message: res.data.msg
                  });
                }
              } else {
                this.$store.state.menu = res.data.userInfo.menu
                this.$router.push({path: '/home'})
              }
            }).catch(err => {
              console.log(err)
            })
          } else {
            return false;
          }
        });
      },
      message() {
        this.$notify({
          title: '账号密码',
          message: '账号密码可以随意填写',
          type: 'warning',
          duration: 6000
        });
      }
    },
    mounted() {
      this.message()
    }
  }
</script>

<style scoped>
  #login {
    width: 100%;
    height: 100%;
    background: #2d3a4b;
  }

  .loginTitle {
    padding-top: 230px;
    font-size: 30px;
    color: #ffffff;
    text-align: center;
    margin-top: 0px;
  }

  .loginBox {
    width: 300px;
    height: 600px;
    margin: 20px auto;
  }

  .sub {
    width: 100%;
  }

  .el-form-item__content .ico {
    position: absolute;
    top: 0px;
    left: 0px;
    z-index: 999;
    width: 40px;
    height: 39px;
    text-align: center;
    line-height: 40px;
    border-right: 1px solid #ccc;
  }

</style>
