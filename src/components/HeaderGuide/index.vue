<template>
  <header class="site-header">
    <div class="navbar">
      <router-link to="/index" class="brand">
        <span class="brand-mark" aria-hidden="true">{{ mode === 'dark' ? '✧' : '樂' }}</span>
        棋剑乐府
      </router-link>
      <el-switch v-model="mode" active-value="dark" inactive-value="light"
        :active-action-icon="Moon" :inactive-action-icon="Sunny" aria-label="深色主题" />
      <nav aria-label="主导航">
        <router-link to="/index" :class="{ current: route.name === 'detail' }">沁园春</router-link>
        <router-link to="/about">青玉案</router-link>
        <template v-if="store.hasLogin">
          <router-link to="/admin">临江仙</router-link>
          <el-avatar :size="32" :src="headImg" alt="JamyKing 头像" />
        </template>
        <router-link v-else to="/login">如梦令</router-link>
      </nav>
    </div>
  </header>
</template>

<script setup>
import { inject } from 'vue'
import { useRoute } from 'vue-router'
import { Moon, Sunny } from '@element-plus/icons-vue'
import headImg from '@/assets/images/head.png'

const route = useRoute()
const store = inject('$store')
const mode = inject('themeMode')
</script>

<style scoped>
.site-header { position: sticky; top: 0; z-index: 20; background: var(--site-nav); border-bottom: 1px solid var(--site-rule); }
.navbar { max-width: 1120px; min-height: 63px; margin: auto; padding-inline: 24px; box-sizing: content-box; display: flex; align-items: center; gap: 24px; }
.brand { display: flex; align-items: center; gap: 12px; color: var(--site-ink); font: 700 26px var(--site-display); letter-spacing: .1em; white-space: nowrap; }
.brand-mark { display: grid; place-items: center; width: 30px; height: 32px; background: var(--site-accent); color: #fffae9; font: 700 21px var(--site-display); outline: 1px solid #edc6a9; outline-offset: -3px; }
html.dark .brand-mark { background: transparent; color: var(--site-accent); outline: none; font: 35px Georgia, serif; }
nav { margin-left: auto; display: flex; align-items: center; gap: clamp(18px, 4vw, 54px); }
nav a { position: relative; padding-block: 19px; white-space: nowrap; font: 600 18px var(--site-display); letter-spacing: .08em; color: var(--site-soft); }
nav :is(.current, .router-link-active, a:hover) { color: var(--site-accent); }
nav :is(.current, .router-link-active)::after { content: ''; position: absolute; width: 22px; height: 2px; bottom: 7px; left: 50%; transform: translateX(-50%); background: currentColor; }
@media (max-width: 620px) {
  .navbar { padding: 8px 14px; min-height: 44px; gap: 8px; flex-wrap: wrap; }
  .brand { font-size: 19px; letter-spacing: 0; gap: 5px; }
  .brand-mark { width: 23px; height: 26px; font-size: 16px; }
  nav { gap: 11px; flex-wrap: wrap; }
  nav a { font-size: 14px; padding-block: 12px; letter-spacing: 0; }
}
</style>
