<template>
  <div class="share-root">
    <el-row :gutter="16">
      <el-col :span="24" v-for="post in posts" :key="post.id">
        <el-card class="post-card">
          <div class="post-head">
            <el-avatar size="40">{{ initials(post.user) }}</el-avatar>
            <div class="meta">
              <div class="user">{{ post.user }}</div>
              <div class="time">{{ post.time }}</div>
            </div>
          </div>
          <div class="post-body" style="margin-top: 8px">{{ post.text }}</div>
          <div class="post-actions" style="margin-top: 8px; color: rgba(0, 0, 0, 0.45)">
            <span style="margin-right: 12px">👍 {{ post.likes }}</span>
            <span>💬 {{ post.comments }}</span>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  setup() {
    const posts = [
      {
        id: 1,
        user: 'reader',
        time: '2小时前',
        text: '今天写了一篇小文章，分享一下学习笔记。',
        likes: 4,
        comments: 2,
      },
      {
        id: 2,
        user: 'writer',
        time: '1天前',
        text: '天气不错，出去拍了几张照片。',
        likes: 10,
        comments: 5,
      },
      {
        id: 3,
        user: 'admin',
        time: '3天前',
        text: '系统维护完成，感谢配合。',
        likes: 2,
        comments: 0,
      },
    ]

    function initials(name = '') {
      return name.slice(0, 2).toUpperCase()
    }
    return { posts, initials }
  },
}
</script>

<style scoped>
.post-card {
  margin-bottom: 12px;
}
.post-head {
  display: flex;
  gap: 12px;
  align-items: center;
}
.meta .user {
  font-weight: 600;
}
.meta .time {
  color: rgba(0, 0, 0, 0.45);
  font-size: 12px;
}
.post-body {
  white-space: pre-wrap;
}
</style>
