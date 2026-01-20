<template>
  <div class="home-root">
    <el-row :gutter="16">
      <el-col :span="8">
        <el-card>
          <div>用户数</div>
          <div style="font-size: 20px; font-weight: 700; margin-top: 8px">{{ users }}</div>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card>
          <div>相册</div>
          <div style="font-size: 20px; font-weight: 700; margin-top: 8px">{{ albums }}</div>
        </el-card>
      </el-col>
      <el-col :span="8">
        <el-card>
          <div>分享数</div>
          <div style="font-size: 20px; font-weight: 700; margin-top: 8px">{{ shares }}</div>
        </el-card>
      </el-col>
    </el-row>

    <div style="margin-top: 16px">
      <el-card>
        <div slot="header">最近动态</div>
        <el-empty description="暂无最近动态" />
      </el-card>
    </div>
  </div>
</template>

<script>
export default {
  setup() {
    // sample counts — replace with real data later
    const users = 3
    const albums = 2
    const shares = 5
    return { users, albums, shares }
  },
}
</script>

<style scoped>
.home-root {
  display: block;
  gap: 12px;
}
</style>
