<template>
  <div class="login_container">
    <el-row>
      <el-col :span="12" :xs="0"></el-col>
      <el-col :span="12" :xs="24">
        <!-- 登录页面表单 -->
        <el-form
          class="login_form"
          :model="loginForm"
          :rules="rules"
          ref="loginForms"
        >
          <h1 class="a">Hello</h1>
          <h2 class="b">欢迎您来到这里！！！</h2>
          <!-- 账号 -->
          <el-form-item prop="username">
            <el-input
              :prefix-icon="User"
              v-model="loginForm.username"
            ></el-input>
          </el-form-item>
          <!-- 密码 -->
          <el-form-item prop="password">
            <el-input
              type="password"
              :prefix-icon="Lock"
              v-model="loginForm.password"
              show-password
            ></el-input>
          </el-form-item>

          <el-form-item>
            <el-button
              class="login_button"
              type="primary"
              size="default"
              @click="login"
            >
              登录
            </el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script setup lang="ts">
import { User, Lock } from '@element-plus/icons-vue'
import { reactive, ref } from 'vue'
import useUserStore from '@/store/modules/user'
import { ElNotification } from 'element-plus'
import { useRouter } from 'vue-router'
import { getTime } from '@/utils/time'
// import { ElNotification } from 'element-plus';

let useStore = useUserStore()
// //获取el-form组件
let loginForms = ref()
// //获取路由器
let $router = useRouter()
// //路由对象
// let $route = useRoute();
// //定义变量控制按钮加载效果
let loading = ref(false)
//收集账号与密码的数据
let loginForm = reactive({ username: 'admin', password: '111111' })
// 登录按钮的点击事件
const login = async () => {
  // 先校验密码是否符合规范再触发登录请求
  await loginForms.value.validate()
  // 加载效果
  loading.value = true
  // 请求成功跳转home
  // 请求失败提示
  try {
    // 保证登录成功
    await useStore.userLogin(loginForm)
    // 跳转首页
    $router.push('/')
    //判断退出前是否在首页
    // let redirect = $router.query.redirect;
    // $router.push({path:redirect||'/'})
    // 提示信息
    ElNotification({
      type: 'success',
      message: '欢迎回来！！！',
      title: `Hi,${getTime()}好`,
    })
    //登录成功加载效果也消失
    loading.value = false
  } catch (error) {
    loading.value = false
    ElNotification({
      type: 'error',
      message: (error as Error).message,
    })
  }
}
//自定义校验规则函数
const validatorUserName = (_rule: any, value: any, callback: any) => {
  //rule:即为校验规则对象
  //value:即为表单元素文本内容
  //函数:如果符合条件callBack放行通过即为
  //如果不符合条件callBack方法,注入错误提示信息
  if (value.length >= 5) {
    callback()
  } else {
    callback(new Error('账号长度至少五位'))
  }
}

const validatorPassword = (_rule: any, value: any, callback: any) => {
  if (value.length >= 6) {
    callback()
  } else {
    callback(new Error('密码长度至少六位'))
  }
}
// 账号密码输入框校验

//     message: 错误提示,
//     trigger: 触发校验方式，change：文本发生变化校验，blur市区焦点校验,
//     min: 最大位数,
//     max:最小位数,
const rules = {
  username: [
    // {
    //   requorod: true,
    //   message: '用户名长度至少6位',
    //   trigger: 'change',
    //   min: 5,
    //   max: 10,
    // },
    { trigger: 'change', validator: validatorUserName },
  ],
  password: [
    // {
    //   requorod: true,
    //   message: '密码长度最大10位',
    //   trigger: 'change',
    //   min: 0,
    //   max: 10,
    // },
    { trigger: 'change', validator: validatorPassword },
  ],
}
</script>

<style scoped lang="scss">
.login_container {
  height: 100vh;
  width: 100%;
  background: (url('@/assets/images/background.jpg')) no-repeat;
  background-size: cover;
}
.login_form {
  background: (url('@/assets/images/login_from.jpg')) no-repeat;
  position: relative;
  width: 80%;
  top: 30vh;
  background-size: cover;
  padding: 40px;
}
.a {
  color: white;
  font-size: 40px;
}
.b {
  font-size: 20px;
  color: white;
  margin: 23px 0px;
}
.login_button {
  width: 100%;
}
</style>
