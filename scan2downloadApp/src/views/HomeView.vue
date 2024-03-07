<template>
  <main>
    <div v-if="isWXClient" class="info-can">
      <div class="arrow-icon">
        <ArrowUpOutlined class="pointer" />
      </div>
      <p>请点击右上角菜单</p>
      <p>选择 “ 使用默认浏览器打开 ” 以下载程序</p>
    </div>
    <div v-else>
      <p>正在等待下载，请稍后...</p>
    </div>
  </main>
</template>

<script setup lang="ts">
import { ref, onBeforeMount } from 'vue'
import { ArrowUpOutlined } from '@ant-design/icons-vue';

const downloadUrl = "rtqrapp_input_key_download_url"

const isWXClient = ref(false)

// 判断是否是微信浏览器打开
function isWX() {
  const userAgentStr = JSON.stringify(navigator.userAgent)
  const _isWX = userAgentStr.includes("MicroMessenger")
  return _isWX;
}

// 主逻辑
onBeforeMount(() => {
  const _isWXClient = isWX()
  // 微信
  if (_isWXClient) {
    // 提示用浏览器打开
    isWXClient.value = true
    setTimeout(() => {
      alert("检测到您是使用微信打开")
    }, 500)
  }
  // 浏览器
  else {
    // 直接跳转下载
    window.location.href = downloadUrl
  }
})
</script>

<style scoped>
.info-can {
  position: relative;
}

.arrow-icon {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  margin: 0 10px;
}

.pointer {
  font-size: 28px;
  color: #3CB371;
  animation: pointing 1.2s linear infinite;
}

p {
  font-size: 15px;
  text-align: center;
}

@keyframes pointing {
  50% {
    transform: translateY(24px);
  }
}
</style>
