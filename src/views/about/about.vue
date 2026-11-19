<template>
  <main class="public-page">
    <atlas-screen />
    <section class="chronicle">
      <h2 class="section-title">项目时间线</h2>
      <el-skeleton :loading="loading" animated :rows="8">
        <el-timeline>
          <el-timeline-item v-for="item in timeList" :key="item.id" :timestamp="item.completeTime" placement="top">
            <el-card shadow="never">
              <div class="project-heading">
                <h3>{{ item.title }}</h3>
                <a v-if="item.github" :href="item.github" aria-label="GitHub 仓库"><i class="iconfont icon-github"></i></a>
                <a v-if="item.link" :href="item.link" aria-label="打开项目"><el-icon><TopRight /></el-icon></a>
              </div>
              <p>{{ item.subtitle }}</p>
              <div v-if="item.images?.length" class="photos">
                <el-image v-for="(image, i) in item.images" :key="image" fit="contain" :src="image"
                  :alt="item.title + ' 项目图 ' + (i + 1)" :preview-src-list="item.images" :initial-index="i" preview-teleported loading="lazy" />
              </div>
            </el-card>
          </el-timeline-item>
        </el-timeline>
      </el-skeleton>
      <el-button v-if="pageInfo.pageNo < totalPage" class="more" @click="loadMores">加载更多　＋</el-button>
    </section>
    <el-backtop />
  </main>
</template>

<script setup>
import {ref, reactive, inject, onBeforeMount} from 'vue'
import { TopRight } from '@element-plus/icons-vue'
import AtlasScreen from '@/components/AtlasScreen/index.vue'

const request = inject('$request')

const loading = ref(true)
const timeList = ref([])
const pageInfo = reactive({
  pageNo: 1,
  pageSize: 5
})
const totalPage = ref(1)

onBeforeMount(() => {
  getTimelineList()
})

const getTimelineList = async () => {
  try {
    const {code, data} = await request({
      url: '/api/timeLine/list',
      method: 'POST',
      data: pageInfo
    })
    if (code === 0) {
      loading.value = false
      totalPage.value = data.totalPage
      for (const item of data.listData) {
        timeList.value.push({
          ...item,
          images: item.images?.map(src => src.replace(/^http:\/\/www\.jianking\.vip\//, 'https://www.jianking.vip/'))
        })
      }
    }
  } catch (err) {
    console.error(err)
  }
}
const loadMores = () => {
  pageInfo.pageNo += 1
  getTimelineList()
}
</script>

<style scoped>
.chronicle { width: min(900px, calc(100% - 40px)); margin: 44px auto 64px; }
.el-timeline { padding-left: 6px; --el-timeline-node-color: var(--site-accent); }
.chronicle :deep(.el-timeline-item__tail) { border-color: var(--site-rule); }
.chronicle :deep(.el-timeline-item__node) { background: var(--site-accent); box-shadow: 0 0 0 3px var(--site-bg); }
.chronicle :deep(.el-timeline-item__timestamp) { font: 14px var(--site-mono); letter-spacing: .06em; color: var(--site-accent); }
.project-heading { display: flex; align-items: center; gap: 12px; }
.project-heading h3 { margin-right: auto; font: 700 clamp(20px, 2vw, 27px)/1.4 var(--site-display); }
.project-heading a { color: var(--site-accent); }
.chronicle p { margin: 12px 0; color: var(--site-soft); font-size: 16px; line-height: 1.8; white-space: pre-line; overflow-wrap: anywhere; }
.photos { display: flex; align-items: center; gap: 12px; flex-wrap: wrap; }
.photos .el-image { width: min(175px, 100%); height: 170px; border: 1px solid var(--site-rule); border-radius: var(--site-radius); background: var(--site-bg); cursor: zoom-in; }
.photos .el-image:only-child { width: min(350px, 100%); }
.more { display: flex; margin: 16px auto 0; min-width: 160px; }
@media (max-width: 600px) {
  .chronicle { width: calc(100% - 28px); margin-block: 28px 40px; }
  .photos .el-image { width: calc(50% - 8px); height: 114px; }
}
</style>
