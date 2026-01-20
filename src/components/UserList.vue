<template>
  <div class="user-list-root">
    <el-row class="toolbar" align="middle" justify="space-between">
      <el-col>
        <el-input
          v-model="q"
          placeholder="搜索用户、邮箱…"
          clearable
          style="width: 300px"
        ></el-input>
        <el-button type="primary" :loading="loading" @click="fetchUsers" style="margin-left: 12px"
          >刷新</el-button
        >
      </el-col>
      <el-col>
        <div class="right">共 {{ users.length }} 位用户</div>
      </el-col>
    </el-row>

    <div v-if="loading" class="loading-wrap">
      <el-skeleton :rows="1" animated>
        <template #template>
          <el-skeleton-item variant="image" style="width: 40px; height: 40px; margin-right: 12px" />
          <el-skeleton-item style="width: 120px" />
        </template>
      </el-skeleton>
    </div>

    <div v-else-if="error" class="error">请求失败：{{ error }}</div>

    <div v-else>
      <div v-if="filtered.length === 0" class="empty muted">没有匹配的用户。</div>

      <el-row :gutter="20" class="grid">
        <el-col :xs="24" :sm="12" :md="12" v-for="user in filtered" :key="user.email">
          <UserCard :user="user" @click="selectUser(user)" />
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'
import UserCard from './UserCard.vue'

export default {
  components: { UserCard },
  setup() {
    const users = ref([])
    const loading = ref(true)
    const error = ref(null)
    const q = ref('')

    async function fetchUsers() {
      loading.value = true
      error.value = null
      try {
        const url = '/users/all'
        const headers = { 'Content-Type': 'application/json' }
        const token = localStorage.getItem('authToken')
        if (token) headers['Authorization'] = `Bearer ${token}`
        const res = await fetch(url, { headers })
        if (!res.ok) throw new Error(`${res.status} ${res.statusText}`)
        const data = await res.json()
        // handle both plain array responses and wrapped responses like { data: [...] }
        const list = Array.isArray(data) ? data : Array.isArray(data.data) ? data.data : []
        users.value = list
      } catch (e) {
        error.value = `${e.message}`
        console.error('fetch /users/all failed:', e)
      } finally {
        loading.value = false
      }
    }

    onMounted(fetchUsers)

    const filtered = computed(() => {
      const term = q.value.trim().toLowerCase()
      if (!term) return users.value
      return users.value.filter(
        (u) =>
          (u.username || '').toLowerCase().includes(term) ||
          (u.email || '').toLowerCase().includes(term),
      )
    })

    function initials(name = '') {
      return name
        .split(' ')
        .map((s) => s[0])
        .join('')
        .slice(0, 2)
        .toUpperCase()
    }

    function selectUser(user) {
      // emit selected user for parent to open detail
      // use $emit via this not available in setup return, so we'll use CustomEvent on root element
      const ev = new CustomEvent('show-user', { detail: user })
      window.dispatchEvent(ev)
    }

    return { users, loading, error, q, filtered, fetchUsers, initials, selectUser }
  },
  components: { UserCard },
}
</script>

<style scoped>
.muted {
  color: #666;
}
.error {
  color: #c33;
  margin: 12px 0;
}
.user-list-root {
  margin-top: 8px;
}
.toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 12px;
  margin-bottom: 12px;
}
.toolbar .left {
  display: flex;
  gap: 8px;
  align-items: center;
}
.search {
  padding: 8px 12px;
  border: 1px solid rgba(27, 31, 47, 0.08);
  border-radius: 8px;
  min-width: 220px;
}
.btn {
  background: #3b82f6;
  color: white;
  border: 0;
  padding: 8px 12px;
  border-radius: 8px;
  cursor: pointer;
}
.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
.right {
  color: rgba(27, 31, 47, 0.6);
}
.loading-wrap {
  display: flex;
  align-items: center;
  gap: 10px;
}
.spinner {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 3px solid rgba(0, 0, 0, 0.12);
  border-top-color: #3b82f6;
  animation: spin 1s linear infinite;
}
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
.grid {
  display: grid;
  /* increase minimum column width so cards are wider */
  /* wider cards so each card has more horizontal space */
  /* using el-row/col layout now — keep grid styles simple */
  grid-template-columns: none;
  gap: 20px;
}
.card {
  display: block;
  padding: 18px;
  background: linear-gradient(180deg, #ffffff, #fbfbff);
  border-radius: 12px;
  box-shadow: 0 6px 20px rgba(27, 31, 47, 0.06);
  overflow: visible;
}
.card-body {
  display: flex;
  align-items: flex-start;
  gap: 12px;
}
.avatar {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: #e6f0ff;
  color: #0f172a;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  line-height: 56px;
  font-size: 18px;
  flex: none;
}
.info {
  flex: 1;
  min-width: 0;
}
.card .info {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
.username {
  font-weight: 600;
  color: var(--text-900, #0f172a);
}
.email {
  color: rgba(27, 31, 47, 0.55);
  font-size: 13px;
}
.empty {
  padding: 18px;
}
</style>
