<template>
  <div class="user-list-root">
    <el-row class="toolbar" align="middle" justify="space-between">
      <el-col>
        <el-input v-model="q" placeholder="搜索用户、账号…" clearable style="width: 320px" />
        <el-button type="primary" :loading="loading" @click="fetchUsers" style="margin-left: 12px"
          >刷新</el-button
        >
      </el-col>
      <el-col>
        <div class="right">共 {{ total }} 位用户</div>
      </el-col>
    </el-row>

    <div v-if="loading" class="loading-wrap">
      <el-skeleton :rows="3" animated />
    </div>

    <div v-else-if="error" class="error">请求失败：{{ error }}</div>

    <div v-else>
      <div v-if="filtered.length === 0" class="empty muted">没有匹配的用户。</div>

      <el-row :gutter="20" class="grid">
        <el-col
          v-for="user in pageData"
          :key="user.account"
          class="user-col"
        >
          <div class="card-wrapper" @click="selectUser(user)">
            <UserCard :user="user" />
          </div>
        </el-col>
      </el-row>

      <div class="pager">
        <el-pagination
          background
          layout="prev, pager, next, sizes, total"
          :page-size="pageSize"
          v-model:current-page="page"
          :page-sizes="[4, 8, 12]"
          :total="total"
          @size-change="onSizeChange"
          @current-change="onPageChange"
        />
      </div>

      <!-- 用户详情弹窗 -->
      <el-dialog
        v-model="dialogVisible"
        title="用户详情"
        width="520px"
      >
        <div class="detail-body" v-if="selectedUser">
          <el-avatar size="96" class="large-avatar">{{
            initials(selectedUser && selectedUser.name)
          }}</el-avatar>
          <div class="meta">
            <h3>{{ selectedUser.name }}</h3>
            <div class="email">账号: {{ selectedUser.account }}</div>
            <div class="info">
              年龄: {{ selectedUser.age }} · 性别:
              {{ selectedUser.gender }} · 角色ID:
              {{ selectedUser.role_id }}
            </div>
            <div class="info">
              密码（仅示例）: {{ selectedUser.password }}
            </div>
          </div>
        </div>
        <template #footer>
          <el-button @click="closeDialog">关闭</el-button>
        </template>
      </el-dialog>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'
import UserCard from './UserCard.vue'

export default {
  components: { UserCard },
  emits: ['show-user'],
  setup(_, { emit }) {
    const users = ref([])
    const loading = ref(true)
    const error = ref(null)
    const q = ref('')

    // 添加弹窗相关状态
    const dialogVisible = ref(false)
    const selectedUser = ref(null)

    // pagination
    const page = ref(1)
    const pageSize = ref(4)

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
      return users.value.filter((u) => {
        const name = (u.name || '').toLowerCase()
        const account = (u.account || '').toLowerCase()
        return name.includes(term) || account.includes(term)
      })
    })

    const total = computed(() => filtered.value.length)

    const pageData = computed(() => {
      const start = (page.value - 1) * pageSize.value
      return filtered.value.slice(start, start + pageSize.value)
    })

    function onPageChange(p) {
      page.value = p
      window.scrollTo({ top: 0, behavior: 'smooth' })
    }
    function onSizeChange(s) {
      pageSize.value = s
      page.value = 1
    }

    function initials(name = '') {
      return (name || '')
        .split(' ')
        .map((s) => s[0])
        .join('')
        .slice(0, 2)
        .toUpperCase()
    }

    function selectUser(user) {
      console.log('UserList: selectUser', user)
      selectedUser.value = user
      dialogVisible.value = true
      emit('show-user', user)
      try {
        const ev = new CustomEvent('show-user', { detail: user })
        window.dispatchEvent(ev)
      } catch (e) {}
    }

    function closeDialog() {
      dialogVisible.value = false
      selectedUser.value = null
    }

    return {
      users,
      loading,
      error,
      q,
      filtered,
      fetchUsers,
      initials,
      selectUser,
      page,
      pageSize,
      pageData,
      total,
      onPageChange,
      onSizeChange,
      dialogVisible,
      selectedUser,
      closeDialog
    }
  },
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
.pager {
  margin-top: 24px;
  text-align: center;
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
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}
.user-col {
  cursor: pointer;
}
.card {
  display: block;
  padding: 18px;
  background: linear-gradient(180deg, #ffffff, #fbfbff);
  border-radius: 12px;
  box-shadow: 0 6px 20px rgba(27, 31, 47, 0.06);
  overflow: visible;
}
.empty {
  padding: 18px;
}
.detail-body {
  display: flex;
  gap: 16px;
  align-items: center;
  padding-top: 12px;
}
.large-avatar {
  background: #e6f0ff;
  color: #0f172a;
}
.meta h3 {
  margin: 0;
}
.info {
  margin-top: 8px;
  color: rgba(0, 0, 0, 0.45);
}
</style>
*** End Patch
