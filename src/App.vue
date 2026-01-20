<template>
  <div>
    <Login v-if="!isAuthenticated" @login-success="onLogin" />

    <div v-else class="app-shell">
      <aside class="side">
        <div class="logo">MyAdmin</div>
        <el-menu :default-active="active" @select="onSelect" class="menu" router="{false}">
          <el-menu-item index="home">首页</el-menu-item>
          <el-menu-item index="share">分享</el-menu-item>
          <el-menu-item index="album">相册</el-menu-item>
          <el-menu-item index="users">用户列表</el-menu-item>
        </el-menu>
      </aside>

      <main class="main">
        <header class="topbar">
          <h2>{{ title }}</h2>
          <div style="float: right; display: flex; align-items: center; gap: 12px">
            <div v-if="currentUser">你好，{{ currentUser.username }}</div>
            <el-button type="text" @click="logout">登出</el-button>
          </div>
        </header>

        <section class="content">
          <component
            :is="currentComponent"
            :user="selectedUser"
            @show-user="openUser"
            @back="closeUser"
          />
        </section>
      </main>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import UserList from './components/UserList.vue'
import Home from './components/Home.vue'
import Share from './components/Share.vue'
import Album from './components/Album.vue'
import Placeholder from './components/Placeholder.vue'
import UserDetail from './components/UserDetail.vue'
import Login from './components/Login.vue'

export default {
  components: { UserList, Placeholder, Login },
  setup() {
    const active = ref('home')
    const map = {
      home: { comp: 'Home', title: '首页' },
      share: { comp: 'Share', title: '分享' },
      album: { comp: 'Album', title: '相册' },
      users: { comp: 'UserList', title: '用户列表' },
    }

    const selectedUser = ref(null)
    const currentUser = ref(
      localStorage.getItem('authUser') ? JSON.parse(localStorage.getItem('authUser')) : null,
    )
    const isAuthenticated = ref(!!currentUser.value)

    const currentComponent = computed(() => {
      if (selectedUser.value) return UserDetail
      if (active.value === 'home') return Home
      if (active.value === 'share') return Share
      if (active.value === 'album') return Album
      return UserList
    })

    const title = computed(() =>
      selectedUser.value ? '用户详情' : (map[active.value] && map[active.value].title) || '',
    )

    function onSelect(key) {
      active.value = key
    }

    function openUser(user) {
      selectedUser.value = user
    }

    function closeUser() {
      selectedUser.value = null
    }

    function onLogin(user) {
      currentUser.value = user
      isAuthenticated.value = true
      // go to home after login
      active.value = 'home'
    }

    function logout() {
      localStorage.removeItem('authUser')
      localStorage.removeItem('authToken')
      currentUser.value = null
      isAuthenticated.value = false
      selectedUser.value = null
    }

    // listen for window-dispatched events from UserList (simpler than prop drilling)
    function onWindowShow(ev) {
      if (ev && ev.detail) openUser(ev.detail)
    }
    window.addEventListener('show-user', onWindowShow)

    // clean up when module unmounts — not strictly necessary in SPA dev flow
    // but keep for good practice
    if (typeof window !== 'undefined') {
      // no-op cleanup here (could remove listener if App is destroyed)
    }

    return {
      active,
      currentComponent,
      title,
      onSelect,
      openUser,
      closeUser,
      selectedUser,
      isAuthenticated,
      onLogin,
      logout,
      currentUser,
    }
  },
}
</script>

<style scoped>
.app-shell {
  display: flex;
  min-height: 80vh;
}
.side {
  width: 220px;
  background: #fff;
  border-right: 1px solid rgba(0, 0, 0, 0.04);
  padding: 18px;
}
.logo {
  font-weight: 700;
  margin-bottom: 12px;
}
.main {
  flex: 1;
  padding: 18px;
}
.topbar {
  border-bottom: 1px solid rgba(0, 0, 0, 0.04);
  padding-bottom: 12px;
  margin-bottom: 12px;
}
.content {
  padding-top: 12px;
}
</style>
