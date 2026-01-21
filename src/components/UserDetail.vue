<template>
  <el-dialog
    v-model="dialogVisible"
    title="用户详情"
    width="520px"
  >
    <template #default>
      <div class="detail-body">
        <el-avatar size="96" class="large-avatar">{{ initials(user && user.name) }}</el-avatar>
        <div class="meta">
          <h3>{{ user ? user.name : '' }}</h3>
          <div class="email">账号: {{ user ? user.account : '' }}</div>
          <div class="info">
            年龄: {{ user ? user.age : '' }} · 性别: {{ user ? user.gender : '' }} · 角色ID:
            {{ user ? user.role_id : '' }}
          </div>
          <div class="info">密码（仅示例）: {{ user ? user.password : '' }}</div>
        </div>
      </div>
    </template>
    <template #footer>
      <el-button @click="closeDialog">关闭</el-button>
    </template>
  </el-dialog>
</template>

<script>
import { computed } from 'vue'

export default {
  name: 'UserDetail',
  props: {
    user: { type: Object, default: null },
    modelValue: { type: Boolean, default: false },  // 使用modelValue替代visible
  },
  emits: ['update:modelValue', 'close'],
  setup(props, { emit }) {
    function initials(name = '') {
      return (name || '').slice(0, 2).toUpperCase()
    }
    
    const dialogVisible = computed({
      get: () => props.modelValue,
      set: (value) => emit('update:modelValue', value)
    });
    
    function closeDialog() {
      emit('update:modelValue', false);
      emit('close');
    }
    
    return { initials, closeDialog, dialogVisible };
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
</style>