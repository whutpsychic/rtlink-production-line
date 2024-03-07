<template>
  <div class="gen-qr-download">
    <el-form ref="ruleFormRef" :model="formState" label-width="auto" style="max-width: 600px" :rules="rules">
      <el-form-item label="APP名称" prop="appname">
        <el-input v-model="formState.appname" />
      </el-form-item>
      <el-form-item label="下载地址" prop="downloadUrl">
        <el-input v-model="formState.downloadUrl" />
      </el-form-item>
      <div style="height: 10px;" />
      <el-form-item>
        <el-button type="primary" :loading="loading" @click="() => submitForm(ruleFormRef)">下载文件包</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive } from 'vue';
import axios from 'axios';
import { saveAs } from 'file-saver';
import JsZip from 'jszip';
import type { FormInstance, FormRules } from 'element-plus';

const htmlUrl: string = "https://raw.githubusercontent.com/whutpsychic/rtlink-production-line/main/publishedPages/scan2download/index.html";
const jsUrl: string = "https://raw.githubusercontent.com/whutpsychic/rtlink-production-line/main/publishedPages/scan2download/assets/index.js";
const cssUrl: string = "https://raw.githubusercontent.com/whutpsychic/rtlink-production-line/main/publishedPages/scan2download/assets/index.css";

const nameKey: string = 'rtqrapp_input_key_appname';
const urlKey: string = 'rtqrapp_input_key_download_url';

const ruleFormRef = ref<FormInstance>();

interface DownloadAppFormState {
  appname: string;
  downloadUrl: string;
}

const formState = reactive<DownloadAppFormState>({
  appname: '',
  downloadUrl: '',
});

const rules = reactive<FormRules>({
  appname: [{ required: true, message: '请填写APP名称！', trigger: 'blur' }],
  downloadUrl: [{ required: true, message: '请填写APP下载地址！', trigger: 'blur' }],
});

const loading = ref<boolean>(false);

const submitForm = async (formEl: FormInstance | undefined) => {
  if (!formEl) return;
  loading.value = true;
  await formEl.validate((valid, fields) => {
    if (valid) {
      console.log('submit!');
      // 开始准备下载
      downloadWebpage()
    } else {
      console.log('error submit!', fields);
      loading.value = false;
    }
  })
}

async function getFile(url: string, type: string): Promise<Blob | undefined> {
  const response = await axios.get(url).catch((err: Error) => {
    console.error(err)
  })
  if (response && response.data) {
    let dataStr = response.data
    // 装填.html
    if (type === 'html') {
      dataStr = dataStr.replace(nameKey, formState.appname);
    }
    // 装填.js
    else if (type === 'js') {
      dataStr = dataStr.replace(urlKey, formState.downloadUrl);
    }
    // 装填.css
    else if (type === 'css') {
    }

    const blob = new Blob([dataStr], { type: "text/plain;charset=utf-8" });
    return blob;
  }
}

const downloadWebpage = async (): Promise<void> => {
  Promise.all([getFile(htmlUrl, 'html'), getFile(jsUrl, 'js'), getFile(cssUrl, 'css')]).then((res) => {
    const blobHtml = res[0]
    const blobJs = res[1]
    const blobCss = res[2]

    const success = blobHtml && blobJs && blobCss

    if (success) {
      let zip = new JsZip();
      zip.file("index.html", blobHtml);

      let assets = zip.folder("assets");
      if (assets) {
        assets.file("index.js", blobJs);
        assets.file("index.css", blobCss);
      }

      // 打包下载
      zip.generateAsync({ type: "blob" })
        .then(function (content) {
          // see FileSaver.js
          saveAs(content, "扫码下载页.zip");
        });
    } else {
      alert("无法连接至Github，请检查网络连接状态！")
      return
    }

  })

  loading.value = false
}

</script>

<style scoped></style>
