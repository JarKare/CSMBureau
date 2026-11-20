<template>
  <footer>
    <nav aria-label="外部链接">
      <a href="https://github.com/JarKare"><i class="iconfont icon-github"></i> GitHub</a>
      <a href="http://demo.jianking.vip"><i class="iconfont icon-demo"></i> Demo</a>
      <a href="https://ext.dcloud.net.cn/publisher?id=187555"><i class="iconfont icon-uniapp"></i> uni-app</a>
    </nav>
    <p>Copyright © {{ year }} JarKare All rights Reserved.　<a rel="nofollow" href="https://beian.miit.gov.cn" target="_blank">浙ICP备20019063号-1</a></p>
    <a class="record" href="http://www.beian.gov.cn/portal/registerSystemInfo?recordcode=33010802010934" target="_blank">
      <img :src="emblemImg" alt="" width="16" height="16">浙公网安备 33010802010934号
    </a>
  </footer>
</template>

<script setup>
import emblemImg from '@/assets/images/record.png'
const year = new Date().getFullYear()
</script>

<style scoped>
footer { padding: 32px 16px; background: var(--site-footer); color: var(--site-footer-text); display: grid; justify-items: center; gap: 7px; text-align: center; font-size: 12px; line-height: 1.65; }
nav { display: flex; align-items: center; margin-bottom: 8px; font: 14px var(--site-mono); }
nav a { padding-inline: clamp(6px, 2vw, 14px); white-space: nowrap; }
nav a + a { border-left: 1px solid #728078; }
a { color: inherit; }
a:hover { text-decoration: underline; }
.record { display: inline-flex; align-items: center; gap: 6px; }
</style>
