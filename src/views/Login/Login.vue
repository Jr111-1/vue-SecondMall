<template>
    <div id="login">
      <div class="header">
        <a href="/"><i class="iconfont icon-B"></i></a>
      </div>
      <div class="content">
        <div class="login-banner-wrap">
          <!--<img src="http://localhost:8082//api//images//users//1624103410129.png">-->
        </div>
        <div class="login-form">
          <div class="form-wrap">
            <div class="login-switch-tab">
              <a href="#"  v-for="(item, index) in menuTab" :key="item.id" :class="{'item-active':item.isActive}" @click="toggleMenu(item, index)">{{ item.txt }}</a>
            </div>
            <el-form :model="loginForm" v-show="menuTab[0].isActive" status-icon :rules="rules" ref="loginForm">
              <el-form-item prop="email" props="email">
                <el-input v-model="loginForm.email" placeholder="用户邮箱" prefix-icon="el-icon-user-solid"></el-input>
              </el-form-item>
              <el-form-item prop="password" props="password">
                <el-input type="password" v-model="loginForm.password" placeholder="请输入登录密码" prefix-icon="el-icon-lock"></el-input>
              </el-form-item>
              <div style="width: 450px;height: 50px;margin-left: 10px;margin-top: 5px">
                <el-input v-model="inputVal" style="width:150px ;" clearable />
                <validate-code ref="ref_validateCode" @change="changeCode"  style="margin-left: 20px; vertical-align:middle " />
                {{result}}
              </div>
              <el-button type="danger" @click="login()">登录</el-button>
<!--              <Vcode :show="isShow" @success="successlogin" @close="closelogin" />-->
            </el-form>
            <el-form :model="registerForm" status-icon :rules="rules" ref="registerForm" v-show="menuTab[1].isActive">
              <el-form-item prop="name">
                <el-input v-model="registerForm.name" placeholder="用户名称" prefix-icon="el-icon-user"></el-input>
              </el-form-item>
              <el-form-item prop="email">
                <el-input v-model="registerForm.email" placeholder="用户邮箱" prefix-icon="el-icon-user-solid"></el-input>
              </el-form-item>
              <el-form-item prop="password">
                <el-input type="password" v-model="registerForm.password" placeholder="请输入登录密码" prefix-icon="el-icon-lock"></el-input>
              </el-form-item>
              <el-form-item prop="checkPassword">
                <el-input type="password" v-model="registerForm.checkPassword" placeholder="请再次输入密码" prefix-icon="el-icon-lock"></el-input>
              </el-form-item>

              <el-form-item prop="xingming">
                <el-input type="xingming" v-model="registerForm.xingming" placeholder="姓名" prefix-icon="el-icon-lock"></el-input>
              </el-form-item>

              <el-form-item prop="sex">
                <el-input type="sex" v-model="registerForm.sex" placeholder="性别" prefix-icon="el-icon-lock"></el-input>
              </el-form-item>

              <el-form-item prop="tel">
                <el-input type="tel" v-model="registerForm.tel" placeholder="电话" prefix-icon="el-icon-lock"></el-input>
              </el-form-item>

              <el-form-item prop="city">
                <el-input type="city" v-model="registerForm.city" placeholder="城市" prefix-icon="el-icon-lock"></el-input>
              </el-form-item>

              <el-form-item prop="bankid">
                <el-input type="bankid" v-model="registerForm.bankid" placeholder="银行卡" prefix-icon="el-icon-lock"></el-input>
              </el-form-item>

              <el-button type="danger" @click="register()">注册</el-button>
              <Vcode :show="isShow" @success="successreg" @close="closereg" />
            </el-form>
            <div class="form-bottom">
              <a href="http://localhost:8081/admin/login">管理员登录</a>
            </div>
          </div>
        </div>
      </div>
    </div>
</template>
<script>
import Vcode from 'vue-puzzle-vcode'
import ValidateCode from '@/components/ValidateCode';
export default {
  components: {
    Vcode,
    ValidateCode
  },
  data () {
    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'))
      } else {
        if (this.registerForm.checkPassword !== '') {
          this.$refs.registerForm.validateField('checkPassword')
        }
        callback()
      }
    }
    var validatePass2 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请再次输入密码'))
      } else if (value !== this.registerForm.password) {
        callback(new Error('两次输入密码不一致!'))
      } else {
        callback()
      }
    }
    var validatePass3 = (rule, value, callback) => {
      const reg = '^\\w+([-+.]\\w+)*@\\w+([-.]\\w+)*\\.\\w+([-.]\\w+)*$'
      if (value === '') {
        callback(new Error('请输入邮箱'))
      } else if (value.search(reg)) {
        callback(new Error('请输入正确的邮箱格式'))
      } else {
        callback()
      }
    }
    var validatePass4 = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入用户名'))
      } else {
        callback()
      }
    }
    var validatePass5 = (rule, value, callback) => {
      if (value.length != 16) {
        callback(new Error('请输入16位'))
      } else {
        callback()
      }
    }
    return {
      isShow: false, // 验证码模态框是否出现
      inputVal: "",
      checkCode: "",
      result: '',
      loginForm: {
        email: '',
        password: ''
      },
      registerForm: {
        name: '',
        email: '',
        password: '',
        checkPassword: '',
        xingming: '',
        sex: '',
        tel: '',
        city: '',
        bankid: ''
      },
      menuTab: [
        { txt: '登录', isActive: true },
        { txt: '注册', isActive: false }
      ],
      rules: {
        password: [
          { validator: validatePass, trigger: 'blur' }
        ],
        checkPassword: [
          { validator: validatePass2, trigger: 'blur' }
        ],
        name: [
          { validator: validatePass4, trigger: 'blur' }
        ],
        email: [
          { validator: validatePass3, trigger: 'blur' }
        ],
        bankid: [
          { validator: validatePass5, trigger: 'blur'}
        ]
      }
    }
  },
  methods: {
    toggleMenu (item, index) {
      if (item.isActive !== true) {
        this.menuTab[1 - index].isActive = item.isActive
        item.isActive = !item.isActive
      }
    },
    // successlogin (msg) {
    //   this.isShow = false // 通过验证后，需要手动隐藏模态框
    //   this.$axios.post('/apis/user', this.loginForm).then((resp) => {
    //     var data = resp.data
    //     if (data.code === 200) {
    //       this.$store.commit('GET_USER', data.data)
    //       this.$router.push({
    //         name: 'Home'
    //       })
    //     } else {
    //       this.$notify.error({
    //         title: '错误',
    //         message: '用户名或密码错误！'
    //       })
    //     }
    //   })
    // },
    // closelogin () {
    //   this.isShow = false
    // },

    login () {
      if (this.inputVal.toUpperCase() === this.checkCode) {
        this.result = "☑";
        this.$axios.post('/apis/user', this.loginForm).then((resp) => {
          var data = resp.data
          if (data.code === 200) {
            this.$store.commit('GET_USER', data.data)
            this.$router.push({
              name: 'Home'
            })
          } else {
            this.$notify.error({
              title: '错误',
              message: '用户名或密码错误！'
            })
          }
        })
      }else {
        this.$message.error("验证码错误")
        this.result = "✘";
        this.inputVal = "";
        this.$refs["ref_validateCode"].draw();
      }

    },
    changeCode(value) {
      this.checkCode = value;
    },

    successreg (msg) {
      this.isShow = false // 通过验证后，需要手动隐藏模态框
      this.$notify({
        title: '成功',
        message: '您已成功注册，请等待管理员审核通过',
        type: 'success'
      })
      this.$axios.post('/apis/users', this.registerForm).then((resp) => {
        const data = resp.data
        if (data.code === 200) {
          this.registerForm = []
          this.menuTab[0].isActive = true
          this.menuTab[1].isActive = false
        }
      })
    },
    closereg () {
      this.isShow = false
    },
    register () {
      this.$axios.post('/apis/users', this.registerForm).then((resp) => {
        const data = resp.data
        if (data.code === 200) {
          this.isShow = true
        } else {
          this.$notify.error({
            title: '错误',
            message: '邮箱已注册！'
          })
        }
      })
    }
  }
}
</script>
<style lang="scss">
#login {
  min-width: 1190px;
  width: 100%;
  padding: 22px 0;
  align-items: center;
  .header {
    margin: 0 18vw;
    height: 44px;
    .icon-B {
      font-size: 35px;
    }
  }
  .content {
    background-color: rgb(198, 235, 253);
    margin-top: 25px;
    display: flex;
    position: relative;

    .login-banner-wrap {
      width: 1190px;
      margin: auto;
      height:800px;
    }

    .login-form {
      width: 350px;
      height: 700px;
      display: flex;
      position: absolute;
      top: 91px;
      right: 275px;
      background-color: #c4ddf3;
      justify-content: center;

      .form-wrap {
        margin: 0 25px;
        width: 100%;

        .login-switch-tab {
          margin-bottom: 20px;
          margin-top: 20px;

          .item-active {
            border-bottom:2px solid #000000;
          }

          a {
            height: 18px;
            line-height: 5px;
            font-size: 16px;
            color: #3c3c3c;
            margin: 9px 20px 0 5px;
            font-weight: 700;
          }
        }

        .el-button--danger {
          width: 100%;
        }

        .form-bottom {
          text-align: right;
          margin-top: 10px;
          a {
            font-size: 12px;
            color: #6c6c6c;
          }
        }
      }
    }
  }
}
</style>
