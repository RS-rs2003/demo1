<template>
  <div class="user-detail">
    <el-card>
      <div class="header">
        <el-button type="text" @click="$emit('back')">← 返回</el-button>
        <div style="flex: 1"></div>
      </div>

      <div class="detail-body">
        <el-avatar size="96" class="large-avatar">{{ initials(user.username) }}</el-avatar>
        <div class="meta">
          <h3>{{ user.username }}</h3>
          <div class="email">{{ user.email }}</div>
          <div class="info">密码（仅示例）: {{ user.password }}</div>
        </div>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  props: { user: { type: Object, required: true } },
  setup(props) {
    function initials(name = '') {
      return name.slice(0, 2).toUpperCase()
    }
    return { initials }
  },
}
</script>

<style scoped>
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
.email {
  color: rgba(0, 0, 0, 0.6);
  margin-top: 6px;
}
.info {
  margin-top: 8px;
  color: rgba(0, 0, 0, 0.45);
}
.header {
  display: flex;
  align-items: center;
  margin-bottom: 6px;
}
</style>
