## 0. vue_js省流

### 结构
```demo
# 重要文件
package.json   // 查看已安装库（bash）
src/main.js   // 导入注册组件
vite.config.js   // 配置路径别名
jsconfig.json   // 配置@路径提示（没有须自建）

# 一般结构
node_modules   // 依赖
src/views   // 主要页面
src/components   // 公共页面
src/router   // 网页路由
src/assets   // 图标,图片等存储路径
src/store   // 状态管理
```

### 安装和使用vue
```bash
npm create vite@latest
# demo为项目名
cd demo
```

### 初始化项目结构
```bash
mkdir src/views src/router src/store
touch src/router/index.js
rm src/style.css
rm src/components/HelloWorld.vue
sed -i "s/import '\.\/style\.css'//g" src/main.js

# 初始内容
cat > src/App.vue << 'EOF'
<script setup>
</script>

<template>
test
</template>

<style scoped>
</style>
EOF
```

### 下载组件
进入项目路径后
```bash
npm install
npm install vue-router@4
# 此处为导入全部
npm install element-plus --save
# 按需导入
# npm install -D unplugin-vue-compon ents unplugin-auto-import
# npm install -D unplugin-icons

# 状态管理库
# npm install pinia
# npm i pinia-plugin-persistedstate
```

### 启动
```bash
npm run dev
```

### src/main.js
```src/main.js
import { createApp } from 'vue'

//整体导入 ElementPlus 组件库
import ElementPlus from 'element-plus' //导入 ElementPlus 组件库的所有模块和功能 
import 'element-plus/dist/index.css' //导入 ElementPlus 组件库所需的全局 CSS 样式
import * as ElementPlusIconsVue from '@element-plus/icons-vue' //导入 ElementPlus 组件库中的所有图标
import zhCn from 'element-plus/dist/locale/zh-cn.mjs' //导入 ElementPlus 组件库的中文语言包

// 导入 router 
import router from './router'

import App from './App.vue'

const app = createApp(App)

//注册 ElementPlus 组件库中的所有图标到全局 Vue 应用中
for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
    app.component(key, component)
}

//app.use(ElementPlus) //将 ElementPlus 插件注册到 Vue 应用中s
app.use(ElementPlus, {
    locale: zhCn // 设置 ElementPlus 组件库的区域语言为中文简体
})

// 将Vue Router实例注册到Vue应用中
app.use(router)

app.mount('#app')
```

### vite.config.js
```vite.config.js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import path from 'path' //导入 node.js path

// https://vite.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
      alias: { //配置路径别名
        '@': path.resolve(__dirname, 'src')
      }
    }
})
```

### jsconfig.json（没有须自建）
```jsconfig.json
{
    "compilerOptions": {
        "baseUrl": ".",
        "paths": {
            "@/*": [
                "src/*"
            ] // 配置 @ 符号指向 src 目录及其子目录
        }
    }
}
```


## 1. 基于Vite创建Vue3项目 
[vite官网](https://cn.vitejs.dev)
```bash
npm create vite@latest
```
+ 选项
```bash
    Ok to proceed? (y) » y
    Project name: » demo
    Select a framework: » Vue
    Select a variant: » JavaScript
```

+ 启动项目
```bash
    # Done. Now run:
    cd demo
    npm install
    npm run dev
```

+ 删除文件
```init
rm src/style.css
rm src/components/HelloWorld.vue
```

+ 删除代码
```init
# main.js <- 文件名
import './style.css' 
```

编辑 src/App.vue 代码
```App.vue
<script setup>
</script>

<template>
内容
</template>

<style scoped>
</style>
```

## 2. Vue3 快速入门4-Pinia
   [Pinia官网](https://pinia.vuejs.org/zh)
```bash
npm install pinia
npm i pinia-plugin-persistedstate
```

## 3. Vue Router 快速掌握
[Vue Router 官网](https://router.vuejs.org/zh)
```bash
npm install vue-router@4
```

## 4. ElementPlus 快速掌握
[ElementPlus 官网](https://element-plus.org/zh-CN)
```bash
npm install element-plus --save
```

* 自动导入
+ 安装 unplugin-vue-components 和 unplugin-auto-import 插件
```bash
npm install -D unplugin-vue-compon ents unplugin-auto-import
```

* 自动导入 图标
+ 安装 unplugin-icons 插件
```bash
npm install -D unplugin-icons
```
