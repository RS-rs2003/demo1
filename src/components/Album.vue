<template>
  <div class="album-root">
    <el-row :gutter="12">
      <el-col :xs="12" :sm="8" :md="6" v-for="img in images" :key="img">
        <el-image
          :src="img"
          style="width: 100%; height: 140px; object-fit: cover; border-radius: 8px"
          fit="cover"
        />
      </el-col>
    </el-row>
  </div>
</template>

<script>
export default {
  setup() {
    // sample images (data URLs or placeholders)
    const images = [
      'https://picsum.photos/400/300?random=1',
      'https://picsum.photos/400/300?random=2',
      'https://picsum.photos/400/300?random=3',
      'https://picsum.photos/400/300?random=4',
    ]
    return { images }
  },
}
</script>

<style scoped>
.album-root {
  padding-top: 6px;
}
</style>
