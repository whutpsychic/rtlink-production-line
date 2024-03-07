<template>
  <header class="top-logo">
    <img alt="" :src="logoImg" />
  </header>
  <main>
    <div class="main-app">
      <el-tabs v-model="activeKey" class="demo-tabs">
        <el-tab-pane label="文本生成二维码" name="first">
          <genQRCode />
        </el-tab-pane>
        <el-tab-pane label="定制扫码下载页" name="second">
          <genQRDownload />
        </el-tab-pane>
      </el-tabs>
    </div>
  </main>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import genQRCode from './genQRCode.vue';
import genQRDownload from './genQRDownload.vue';
import logoImg from '@/assets/logo.png';

const activeKey = ref('first');
</script>

<style scoped>
main {
  display: flex;
  justify-content: center;
}

.main-app {
  width: 100%;
  max-width: 1280px;
}

.top-logo {
  height: 60px;
  display: flex;
  align-items: center;
  padding: 0 40px;
}

.top-logo img {
  height: 30px;
}
</style>