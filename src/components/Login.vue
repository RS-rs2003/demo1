<template>
  <div class="login-root">
    <el-card class="login-card">
      <h3 style="margin-bottom: 12px">登录</h3>
      <el-form :model="form" :rules="rules" ref="formRef" label-position="top">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="form.username" autocomplete="username" />
        </el-form-item>

        <el-form-item label="密码" prop="password">
          <el-input v-model="form.password" type="password" autocomplete="current-password" />
        </el-form-item>

        <div style="display: flex; gap: 8px; align-items: center; margin-top: 8px">
          <el-button type="primary" :loading="loading" @click="submit">登录</el-button>
          <el-button @click="clear">清除</el-button>
          <div v-if="error" style="color: #c33; margin-left: 8px">{{ error }}</div>
        </div>
      </el-form>
    </el-card>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'Login',
  emits: ['login-success'],
  setup(_, { emit }) {
    const formRef = ref(null)
    const form = ref({ username: '', password: '' })
    const loading = ref(false)
    const error = ref(null)

    const rules = {
      username: [{ required: true, message: '请输入用户名', trigger: 'blur' }],
      password: [{ required: true, message: '请输入密码', trigger: 'blur' }],
    }

    async function submit() {
      error.value = null
      try {
        // proper async validation with Element Plus
        await formRef.value.validate()
      } catch (vErr) {
        return
      }

      loading.value = true
      try {
        // Try JSON POST first
        let res = await fetch('/users/login', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(form.value),
        })

        // If backend rejects method, try form-urlencoded POST
        if (res.status === 405) {
          res = await fetch('/users/login', {
            method: 'POST',
            headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
            body: new URLSearchParams(form.value).toString(),
          })
        }

        // If still 405, try GET with query params
        if (res.status === 405) {
          const qp = new URLSearchParams(form.value).toString()
          res = await fetch(`/users/login?${qp}`, { method: 'GET' })
        }

        if (!res.ok) {
          // try to read json message, fallback to text
          let bodyText = ''
          try {
            const j = await res.json()
            bodyText = j && (j.message || JSON.stringify(j))
          } catch {
            try {
              bodyText = await res.text()
            } catch {}
          }
          throw new Error(bodyText || `${res.status} ${res.statusText}`)
        }

        const data = await res.json().catch(() => null)
        const payload = (data && (data.data || data.user)) || {}
        const user = payload.user || payload || { username: form.value.username }
        const token = payload.token || (data && data.token) || null
        if (token) localStorage.setItem('authToken', token)
        localStorage.setItem('authUser', JSON.stringify(user))
        emit('login-success', user)
      } catch (e) {
        error.value = e.message || '请求失败'
      } finally {
        loading.value = false
      }
    }

    function clear() {
      form.value.username = ''
      form.value.password = ''
      error.value = null
    }

    return { formRef, form, loading, error, rules, submit, clear }
  },
}
</script>

<style scoped>
.login-root {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 60vh;
}
.login-card {
  width: 360px;
  padding: 18px;
}
</style>
