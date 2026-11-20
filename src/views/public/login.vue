<template>
  <main class="public-page">
    <atlas-screen :saying-show="false">
      <section class="login-form site-panel">
        <img class="head" :src="headImg" alt="JarKare 头像">
        <h1>登录</h1>
        <el-form ref="loginDom" :model="loginForm" :rules="rules" label-width="72px" @submit.prevent="toLogin">
          <el-form-item label="用户名" prop="username">
            <el-input v-model="loginForm.username" autocomplete="username" clearable :prefix-icon="User" placeholder="请输入登录用户名" />
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input v-model="loginForm.password" autocomplete="current-password" type="password" show-password
              :prefix-icon="Lock" placeholder="请输入登录密码" />
          </el-form-item>
          <slide-verify ref="verifyRef" :key="verifyWidth" :w="verifyWidth" :h="130" :imgs="[nightImage]"
            slider-text="向右滑动完成验证" @success="verifySuccess" @refresh="verifyFail" @fail="verifyFail" />
          <el-button class="login-btn" native-type="submit">登　录</el-button>
        </el-form>
      </section>
    </atlas-screen>
  </main>
</template>

<script setup>
import {ref, reactive, inject, useTemplateRef, onBeforeMount, watch, computed} from 'vue'
import AtlasScreen from '@/components/AtlasScreen/index.vue'
import headImg from '@/assets/images/head.png'
import nightImage from '@/assets/images/night.jpg'
import { useMediaQuery } from '@vueuse/core'
import { User, Lock } from '@element-plus/icons-vue'
import SlideVerify from 'vue3-slide-verify'
import "vue3-slide-verify/dist/style.css";
import {useRouter} from 'vue-router'
import { ElMessage } from 'element-plus'

const store = inject('$store')
const request = inject('$request')
const router = useRouter()

const verifyCode = ref(false)
const smallScreen = useMediaQuery('(max-width: 400px)')
const verifyWidth = computed(() => smallScreen.value ? 250 : 310)
watch(verifyWidth, () => { verifyCode.value = false })

const loginForm = reactive({
  username: '',
  password: ''
})
const rules = reactive({
  username: [
    {required: true, message: '请输入用户名', trigger: 'blur'}
  ],
  password: [
    {required: true, message: '请输入密码', trigger: 'blur'}
  ]
})

const loginDom = useTemplateRef('loginDom')
const verifyRef = useTemplateRef('verifyRef')

onBeforeMount(() => {
  store.logout()
})

const verifySuccess = (e) => {
  verifyCode.value = true
}

const verifyFail = () => {
  verifyCode.value = false
}

const toLogin = () => {
  loginDom.value.validate(async (valid) => {
    if (valid && verifyCode.value) {
      try {
        const {code, data} = await request({
          url: '/api/user/login',
          method: 'POST',
          data: loginForm
        })
        if (code === 0) {
          store.login(data.username)
          ElMessage({
            message: '登录成功！',
            type: 'success',
            plain: true,
            duration: '1000',
            onClose: () => {
              router.push({name: 'admin'})
            }
          })
        } else {
          ElMessage({
            message: '用户名或密码错误！',
            type: 'error',
            plain: true,
          })
          verifyCode.value = false
          verifyRef.value.refresh()
        }
      } catch (err) {
        ElMessage({
          message: err,
          type: 'error',
          plain: true,
        })
      }
    } else {
      ElMessage({
        message: '请正确输入登录信息和通过图形验证！',
        type: 'warning',
        plain: true,
      })
    }
  })
}
</script>

<style scoped>
.login-form { box-sizing: border-box; width: min(510px, 100%); min-width: 0; padding: 30px clamp(16px, 3.2vw, 44px) 36px; text-align: center; box-shadow: 0 12px 44px #12212a29; }
.head { width: 70px; height: 70px; border: 3px solid var(--site-card); outline: 1px solid var(--site-rule); border-radius: 50%; object-fit: cover; }
h1 { font: 700 26px var(--site-display); margin: 8px 0 23px; letter-spacing: .1em; }
.el-form { --el-text-color-regular: var(--site-soft); --el-fill-color-blank: var(--site-bg); --el-border-color: var(--site-rule); --el-input-focus-border-color: var(--site-accent); }
.el-input { --el-input-height: 42px; }
.login-form :deep(.el-form-item__label) { align-items: center; justify-content: flex-start; white-space: nowrap; }
.slide-verify { margin: 20px auto 0; }
.login-form :deep(.slide-verify-slider) { box-sizing: border-box; margin-top: 0; background: var(--site-bg); border-color: var(--site-rule); color: var(--site-soft); font-size: 13px; }
.login-form .login-btn { margin-top: 20px; width: min(310px, 100%); height: 44px; background: var(--site-accent); color: var(--site-card); border-color: var(--site-accent); font-weight: 700; }
</style>
