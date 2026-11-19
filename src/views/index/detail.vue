<template>
  <main class="public-page reader site-panel">
    <el-skeleton :loading="loading" animated :rows="12">
      <article>
        <h1>{{ blogDetail.title }}</h1>
        <div class="site-meta">
          <time>{{ blogDetail.createTime }}</time><el-icon><View /></el-icon>{{ blogDetail.clickViews + 1 }}
        </div>
        <div v-html="blogDetail.htmlRender" class="markdown-body"></div>
      </article>
    </el-skeleton>
  </main>
</template>

<script setup>
import {ref, inject, onBeforeMount} from 'vue'
import {useRoute} from 'vue-router'
import { View } from '@element-plus/icons-vue'
import '@/assets/css/github-markdown.css'

const request = inject('$request')
const route = useRoute()

const id = route.query?.id
const loading = ref(true)
const blogDetail = ref({
  createTime: '',
  clickViews: 0,
  htmlRender: ''
})

onBeforeMount(() => {
  loadDetail()
})

const loadDetail = async () => {
  try {
    loading.value = true
    const {code, data} = await request({
      url: '/api/blog/detail',
      method: 'GET',
      data: { id: id }
    })
    if (code === 0) {
      blogDetail.value = data
    }
    loading.value = false
  } catch (err) {
    console.error(err)
  }
}
</script>

<style scoped>
.reader { width: min(1080px, calc(100% - 48px)); box-sizing: border-box; margin: 30px auto 48px; padding: clamp(24px, 4.6vw, 66px) clamp(20px, 5vw, 70px); }
.reader article { max-width: 820px; margin: auto; min-width: 0; }
.reader h1 { font: 700 clamp(29px, 3.1vw, 44px)/1.4 var(--site-display); overflow-wrap: anywhere; }
.site-meta { margin: 18px 0 30px; padding-bottom: 27px; border-bottom: 1px solid var(--site-rule); }
.reader .markdown-body { min-height: 360px; color: var(--site-soft); background: transparent; font-size: clamp(16px, 1.4vw, 18px); line-height: 1.85; overflow-wrap: anywhere; }
.markdown-body :deep(h2), .markdown-body :deep(h3) { font-family: var(--site-display); color: var(--site-ink); }
.markdown-body :deep(ul li) { list-style: disc; }
.markdown-body :deep(ol li) { list-style: decimal; }
.markdown-body :deep(img) { max-width: 100%; height: auto; }
.markdown-body :deep(pre) { padding: 20px; overflow-x: auto; background: var(--site-code); color: var(--site-code-ink); border-radius: var(--site-radius); }
.markdown-body :deep(code) { color: var(--site-accent); background: var(--site-wash); }
.reader .markdown-body :deep(pre code) { background: transparent; color: inherit; }
.markdown-body :deep(.hljs-keyword) { color: #efbf98; }
.markdown-body :deep(.hljs-string) { color: #b9d8ae; }
html.dark .markdown-body :deep(.hljs-keyword) { color: #c3b7ff; }
html.dark .markdown-body :deep(.hljs-string) { color: #8be2d5; }
@media (max-width: 600px) { .reader { width: calc(100% - 24px); margin-block: 16px 28px; } }
</style>
