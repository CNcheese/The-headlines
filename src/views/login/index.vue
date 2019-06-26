<template>
  <div class="login-wrap">
    <div class="login-form-wrap">
      <div class="login-head">
        <img src="./logo_index.png" alt="黑马头条">
      </div>
      <div class="login-form">
        <el-form ref="ruleform" :model="form" :rules="rules">
          <el-form-item prop="mobile">
            <el-input v-model="form.mobile" placeholder="手机号"></el-input>
          </el-form-item>
          <el-form-item prop="code">
            <el-col :span="10">
              <el-input v-model="form.code" placeholder="验证码"></el-input>
            </el-col>
            <el-col :span="10" :offset="2">
              <el-button
              @click="handleSendCode"
              :disabled="!!timer || Loading"
              >{{ timer ? `剩余${codeSecons}秒` :'获取验证码' }}</el-button>
            </el-col>
          </el-form-item>
            <el-form-item prop="age">
              <el-checkbox v-model="form.age" class="check"></el-checkbox>
            <span>我以阅读并同意 <a href="#">用户协议</a>和 <a href="#">隐私条款</a></span>
            </el-form-item>
          <el-form-item>
            <el-button
            type="primary"
            @click="handleLogin"
            :loading="loginLoading"
            class="btn-login"
            >登录</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
// import axios from 'axios'
import '@/vendor/gt'
const initCodeSeconds = 60
export default {
  name: 'Applogin',
  data () {
    return {
      form: {
        mobile: '18822309291',
        code: '',
        age: ''
      },
      loginLoading: false,
      rules: {
        mobile: [
          { required: true, message: '请输入手机号', trigger: 'blur' },
          { len: 11, message: '长度必须为11位', trigger: 'blur' }
        ],
        code: [
          { required: true, message: '请输入验证码', trigger: 'blur' },
          { len: 6, message: '长度必须为6个位', trigger: 'blur' }
        ],
        age: [
          { required: true, message: '请同意用户协议', trigger: 'change' },
          { pattern: /true/, message: '请同意用户协议', trigger: 'change' }
        ]
      },
      captchaObj: null,
      codeSecons: initCodeSeconds,
      timer: null,
      Loading: false,
      sendMobile: ''
    }
  },
  methods: {
    // 表单验证登录
    handleLogin () {
      this.$refs['ruleform'].validate(valid => {
        if (!valid) {
          return
        }
        this.submitLogin()
      })
    },
    // 登录验证
    submitLogin () {
      this.loginLoading = true
      this.$http({
        method: 'POST',
        url: '/authorizations',
        data: this.form
      }).then(data => {
        window.localStorage.setItem('user_info', JSON.stringify(data))
        this.$message({
          message: '登录成功',
          type: 'success'
        })
        this.loginLoading = false
        this.$router.push({
          name: 'home'
        })
      }).catch(err => {
        if (err.response.status === 400) {
          this.$message.error('登录失败，手机号或者验证码错误，请重新登录')
        }
        this.loginLoading = false
      })
    },
    // 校验手机号
    handleSendCode () {
      this.$refs['ruleform'].validateField('mobile', errorMessage => {
        if (errorMessage.trim().length > 0) {
          return
        }
        if (this.captchaObj) {
          if (this.form.mobile !== this.sendMobile) {
            document.body.removeChild(document.querySelector('.geetest_panel'))
            this.showGeetest()
          } else {
            this.captchaObj.verify()
          }
        } else {
          this.showGeetest()
        }
      })
    },
    showGeetest () {
      // const { mobile } = this.form
      // if (this.captchaObj) {
      //   return this.captchaObj.verify()
      // }
      this.Loading = true
      this.$http({
        method: 'GET',
        url: `/captchas/${this.form.mobile}`
      }).then(data => {
        // const data = res.data.data
        window.initGeetest({
          gt: data.gt,
          challenge: data.challenge,
          offline: !data.success,
          new_captcha: data.new_captcha,
          product: 'bind'
        }, (captchaObj) => {
          this.captchaObj = captchaObj
          captchaObj.onReady(() => {
            this.sendMobile = this.form.mobile
            captchaObj.verify()
            this.Loading = false
          }).onSuccess(() => {
            const {
              geetest_challenge: challenge,
              geetest_seccode: seccode,
              geetest_validate: validate } =
            captchaObj.getValidate()
            // 短信验证接口
            this.$http({
              method: 'GET',
              url: `/sms/codes/${this.form.mobile}`,
              params: {
                challenge,
                seccode,
                validate
              }
            }).then(data => {
              this.codeCountDown()
            })
          })
        })
      })
    },
    // 倒计时
    codeCountDown () {
      this.timer = window.setInterval(() => {
        this.codeSecons--
        if (this.codeSecons <= 0) {
          this.codeSecons = initCodeSeconds
          window.clearInterval(this.timer)
          this.timer = null
        }
      }, 1000)
    }
  }
}
</script>

<style  scoped lang='less'>
.login-wrap {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #ccc;
  background:url(./001.jpg)no-repeat;
  .login-head {
    display: flex;
    justify-content: center;
    margin-bottom: 10px;
    img {
      width: 259px;
    }
  }
  .login-form-wrap {
    background-color: #fff;
    padding: 40px;
    border-radius: 15px;
    .btn-login {
      width: 100%;
    }
  }
}
.check {
  margin-left: 10px;
  margin-right: 10px;
}
</style>
