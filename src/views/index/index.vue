<template>
  <main class="public-page">
    <atlas-screen />
    <div class="index-content">
      <section>
        <h2 class="section-title">{{ mode === 'dark' ? '文章索引' : '文章手记' }}</h2>
        <router-link v-for="item in dataList" :key="item.id" class="article site-panel"
          :to="{ name: 'detail', query: { id: item.id } }" @click="item.clickViews += 1">
          <div class="article-heading">
            <h3>{{ item.title }}</h3>
            <el-icon class="open-icon"><TopRight /></el-icon>
          </div>
          <div v-if="item.category?.length" class="tag-group">
            <span v-for="tag in item.category" :key="tag.name" class="article-tag">{{ tag.name }}</span>
          </div>
          <p class="subtitle">{{ item.subtitle }}</p>
          <div class="site-meta">
            <time>{{ item.createTime }}</time><el-icon><View /></el-icon>{{ item.clickViews ?? 0 }}
          </div>
        </router-link>
        <div v-if="dataList.length" class="pager">
          <el-button @click="prePage" :disabled="pageInfo.pageNo === 1" :icon="ArrowLeft">上一页</el-button>
          <el-button @click="nextPage" :disabled="pageInfo.pageNo >= totalPage">下一页<el-icon><ArrowRight /></el-icon></el-button>
        </div>
      </section>
      <aside class="site-panel">
        <img class="portrait" :src="headImg" alt="JamyKing 头像">
        <h3>JamyKing</h3>
        <h4>文章分类</h4>
        <div class="tag-group">
          <button :aria-pressed="!pageInfo.categoryName" @click="tagSelected('')">ALL</button>
          <button v-for="tag in store.category" :key="tag.id"
            :aria-pressed="pageInfo.categoryName === tag.name" @click="tagSelected(tag.name)">{{ tag.name }}</button>
        </div>
      </aside>
    </div>
    <el-backtop />
  </main>
</template>

<script setup>
import {ref, reactive, inject, onBeforeMount} from 'vue'
import AtlasScreen from '@/components/AtlasScreen/index.vue'
import { ElMessage } from 'element-plus'
import { View, ArrowLeft, ArrowRight, TopRight } from '@element-plus/icons-vue'
import headImg from '@/assets/images/head.png'

const store = inject('$store')
const request = inject('$request')
const mode = inject('themeMode')

const dataList = ref([])
const pageInfo = reactive({
  pageNo: 1,
  pageSize: 5,
  categoryName: ''
})
const totalPage = ref(1)

onBeforeMount(() => {
  console.log('Index Mounted')
  getDataList()
  if (store.category.length === 0) {
    getCategoryList()
  }
})

const getDataList = async () => {
  try {
    const {code, data} = await request({
      url: '/api/blog/indexList',
      method: 'POST',
      data: pageInfo
    })
    if (code === 0) {
      totalPage.value = data.totalPage
      dataList.value = data.listData
      if (totalPage.value === 0) {
        ElMessage({
          message: '没有找到合适的内容，请更换条件后再次搜索~',
          type: 'warning',
          plain: true,
        })
      }
    }
  } catch (err) {
    console.error(err)
  }
}

const getCategoryList = async () => {
  try {
    const cateRes = await request({
      url: '/api/category/list',
      method: 'GET'
    })
    if (cateRes.code === 0) {
      store.setCategory(cateRes.data)
    }
  } catch (err) {
    console.error(err)
  }
}

const tagSelected = (name) => {
  pageInfo.categoryName = name
  pageInfo.pageNo = 1
  getDataList()
}

const prePage = () => {
  pageInfo.pageNo -= 1
  getDataList()
}

const nextPage = () => {
  pageInfo.pageNo += 1
  getDataList()
}
</script>

<style scoped>
.index-content { width: min(1120px, calc(100% - 48px)); margin: 44px auto 64px; display: grid; grid-template-columns: minmax(0, 800px) minmax(200px, 280px); align-items: start; gap: clamp(20px, 3vw, 40px); }
.article { display: block; padding: 24px 30px; margin-bottom: 16px; overflow-wrap: anywhere; }
.article:hover, .article:focus-visible { border-color: var(--site-accent); }
.article-heading, .pager { display: flex; align-items: center; justify-content: space-between; gap: 12px; }
.article-heading h3 { font: 700 clamp(21px, 2.4vw, 27px)/1.4 var(--site-display); }
.open-icon { border: 1px solid var(--site-rule); border-radius: 50%; padding: 7px; color: var(--site-accent); flex: none; }
.tag-group { display: flex; gap: 8px; flex-wrap: wrap; }
.article .tag-group { margin-top: 10px; }
.article-tag, .tag-group button { font-family: inherit; font-size: 13px; font-weight: 500; line-height: 1.3; padding: 4px 10px; border-radius: var(--site-radius); }
.article-tag { background: var(--site-wash); color: var(--site-accent); }
.subtitle { color: var(--site-soft); font-size: 16px; line-height: 1.7; margin: 10px 0 15px; }
.pager { margin-top: 24px; }
aside { padding: 18px; }
.portrait { display: block; width: 100%; aspect-ratio: 1; object-fit: contain; background: var(--site-bg); border-radius: var(--site-radius); }
aside h3 { font: 700 24px Georgia, serif; margin: 15px 0; }
html.dark aside h3 { font: 600 21px var(--site-mono); }
aside h4 { border-top: 1px solid var(--site-rule); padding-top: 16px; margin-bottom: 12px; font: 600 16px var(--site-display); }
.tag-group button { border: 1px solid var(--site-rule); background: transparent; color: var(--site-soft); cursor: pointer; }
.tag-group button[aria-pressed=true] { color: var(--site-card); background: var(--site-ink); }
html.dark .tag-group button[aria-pressed=true] { background: var(--site-accent); }
@media (max-width: 760px) {
  .index-content { width: calc(100% - 32px); grid-template-columns: minmax(0, 1fr); margin: 28px auto 40px; }
  .article { padding: 18px; }
  .portrait { width: min(140px, 100%); }
}
</style>
