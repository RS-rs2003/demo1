<template>
  <div class="placeholder">
    <el-empty description="模块未实现（前端占位）">
      <template #image>
        <el-icon><i class="el-icon-s-platform"></i></el-icon>
      </template>
    </el-empty>
    <div style="margin-top: 12px; color: var(--muted-200)">
      这是一个占位模块，后端尚未完成，当前显示示例数据。
    </div>
  </div>
</template>

<script>
export default {
  name: 'Placeholder',
}
</script>

<style scoped>
.placeholder {
  padding: 24px;
  background: #fff;
  border-radius: 8px;
}
</style>
