<template>
  <el-card class="user-card" shadow="hover">
    <div class="user-card-inner">
      <el-avatar size="64" class="avatar">{{ initials(user.name) }}</el-avatar>
      <div class="content">
        <div class="title-row">
          <div class="username">{{ user.name }}</div>
          <el-tag type="info" size="small" v-if="user.account">{{ user.account }}</el-tag>
        </div>

        <el-descriptions size="small" column="1" class="desc">
          <el-descriptions-item label="账号">{{ user.account }}</el-descriptions-item>
          <el-descriptions-item label="密码">{{ user.password }}</el-descriptions-item>
        </el-descriptions>
      </div>
    </div>
  </el-card>
</template>

<script>
export default {
  name: 'UserCard',
  props: { user: { type: Object, required: true } },
  setup(props) {
    function initials(name = '') {
      return (name || '').slice(0, 2).toUpperCase()
    }
    return { initials }
  },
}
</script>

<style scoped>
.user-card {
  cursor: pointer;
}
.user-card-inner {
  display: flex;
  gap: 12px;
  align-items: flex-start;
}
.avatar {
  flex: none;
}
.content {
  flex: 1;
  min-width: 0;
}
.title-row {
  display: flex;
  align-items: center;
  gap: 8px;
  justify-content: space-between;
}
.username {
  font-weight: 700;
}
.desc {
  margin-top: 8px;
}
</style>
