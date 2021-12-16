<template>
  <el-row type="flex" class="row-bg" justify="center">
    <el-col :span="6" :xl="6" :lg="7">
      <h2>CSUN Library Management System</h2>
      <el-image :src="require('@/assets/logo2.svg')"></el-image>
      <h3>Course #586 Author-Hao Lu </h3>
    </el-col>
    <el-col :span="1">

      <el-divider direction="vertical"></el-divider>

    </el-col>
    <el-col :span="6" :xl="6" :lg="7">
      <el-form :model="loginForm" :rules="rules" ref="loginForm" label-width="100px" class="demo-ruleForm">
        <el-form-item label="Username" prop="username" style="width: 400px">
          <el-input v-model="loginForm.username"></el-input>
        </el-form-item>
        <el-form-item label="Password" prop="password" style="width: 400px">
          <el-input v-model="loginForm.password" type="password"></el-input>
        </el-form-item>
        <el-form-item label="Code" prop="code" style="width: 400px">
          <el-input v-model="loginForm.code" style="width: 172px;float: left;margin-right: 8px"></el-input>
          <!--captchaImg should not in loginForm-->
          <el-image :src="captchaImg" class="captchaImg" style="width: 120px" @click="getCaptchaImg()"></el-image>
        </el-form-item>
        <el-form-item style="width: 400px">
          <el-button type="primary" @click="submitForm('loginForm')" style="float: left">Sign In</el-button>
          <el-button @click="resetForm('loginForm')" style="float: left">Reset</el-button>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<script>
import qs from 'qs'
export default {
  name: 'Login',
  data () {
    return {
      captchaImg: null,
      loginForm: {
        username: '',
        password: '',
        code: '',
        token: ''
      },
      rules: {
        username: [
          {
            required: true,
            message: 'Name cannot be empty',
            trigger: 'blur'
          },
          {
            min: 5,
            max: 20,
            message: 'Length of chars should be more than 5 and less than 20',
            trigger: 'blur'
          }
        ],
        password: [
          {
            required: true,
            message: 'Password cannot be empty',
            trigger: 'blur'
          },
          {
            min: 5,
            max: 20,
            message: 'Length of chars should be more than 5 and less than 20',
            trigger: 'blur'
          }
        ],
        code: [
          {
            required: true,
            message: 'Code cannot be empty',
            trigger: 'blur'
          },
          {
            min: 5,
            max: 5,
            message: 'Length of chars should be 5',
            trigger: 'blur'
          }
        ]
      }

    }
  },
  methods: {
    submitForm (formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.$axios.post('/login?' + qs.stringify(this.loginForm)).then(res => {
            const jwt = res.headers.authorization
            this.$store.commit('SET_TOKEN', jwt)
            this.$router.push('/index')
          })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    resetForm (formName) {
      this.$refs[formName].resetFields()
    },
    getCaptchaImg () {
      this.$axios.get('/captcha').then(res => {
        console.log(res.data.data.captchaImg)
        this.loginForm.token = res.data.data.token
        this.captchaImg = res.data.data.captchaImg
        this.loginForm.code = ''
      })
    }
  },
  created () {
    this.getCaptchaImg()
  }
}
</script>

<style scoped>

.el-row {
  background-color: #fafafa;
  height: 100vh;
  display: flex;
  align-items: center;
  text-align: center;
}

.el-divider {
  height: 300px
}
</style>
