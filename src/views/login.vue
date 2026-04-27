<template>
    <div class="container">
        <div class="title">
            <div class="back-home">
                <el-icon><Back /></el-icon>
                <span>返回首页</span>
            </div>
            <div class="title-text">
                <h2>登录您的账户</h2>
                <p>请输入您的登录信息</p>
            </div>
        </div>
        <div class="form-container">
            <div>
                <el-form
                    :model="formData"
                    :rules="rules"
                    ref="ruleFormRef"
                    label-position="top"
                >
                    <el-form-item label="用户名或邮箱" prop="username">
                        <el-input v-model="formData.username" placeholder="请输入用户名" size="large" />
                    </el-form-item>
                    <el-form-item label="密码" prop="password">
                        <el-input v-model="formData.password" placeholder="请输入密码" size="large" type="password" show-password />
                    </el-form-item>
                    <el-form-item>
                        <el-button  class="login-btn" type="primary" @click="handleLogin(ruleFormRef)">登录账户</el-button>
                    </el-form-item>
                </el-form>
                <div class="footer">
                    <p>还没有账户？<RouterLink to="/auth/register">去注册</RouterLink></p>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { login } from '@/api/login'
import { useRouter } from 'vue-router'


const ruleFormRef = ref()
const formData = reactive({
    username: '',
    password: ''
})

const rules = reactive({
    username: [
        { required: true, message: '请输入用户名或邮箱', trigger: 'blur' }
    ],
    password: [
        { required: true, message: '请输入密码', trigger: 'blur' }
    ]
})

const router = useRouter()
const handleLogin = async (formEl) => {
    
    if(!formEl) return
    await formEl.validate((valid,fields) => {
        if(valid) {
            login(formData).then(data => {
                // 登录失败处理
                if(!data || !data.token){
                    return console.error('登录失败')
                }
                // 登录成功，信息保存
                localStorage.setItem('token', data.token)
                localStorage.setItem('userInfo', JSON.stringify(data.userInfo))
                // 登录成功跳转
                if (data.userInfo.userType === 2){
                    router.push("/back/dashboard")
                }
            })
        } 
    })

}

</script>

<style scoped lang="scss">
  .container {
    width: 384px;
    .title{
        .back-home{
            margin-bottom: 60px;
        }
        .title-text{
            text-align: center;
            h2 {
                font-size: 36px;
                margin-bottom: 10px;
            }
            p{
                font-size: 18px;
                color: #6b7280;
            }
        }
    }
    .form-container{
        margin-top: 30px;
        margin-bottom: 60px;
        .login-btn{
            margin-top: 40px;
            width: 100%;
        }
        .footer {
            padding: 30px;
            text-align: center;
        }
    }
  }
</style>