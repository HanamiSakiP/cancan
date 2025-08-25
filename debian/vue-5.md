## 0. 下载和导入ElementPlus 组件库

* [ElementPlus 官网](https://element-plus.org/zh-CN/)

* [ElementPlus 图标官网](https://element-plus.org/zh-CN/component/icon.html#icon-collection)

+ 导入所有
```bash
npm install element-plus --save
```

+ 按需导入
```bash
#自动导入
#安装 unplugin-vue-components 和 unplugin-auto-import 插件
npm install -D unplugin-vue-components unplugin-auto-import
#自动导入 图标
#安装 unplugin-icons 插件
npm install -D unplugin-icons
```

导入所有
src/main.js
```src/main.js
import { createApp } from 'vue'

//整体导入 ElementPlus 组件库
import ElementPlus from 'element-plus' //导入 ElementPlus 组件库的所有模块和功能 
import 'element-plus/dist/index.css' //导入 ElementPlus 组件库所需的全局 CSS 样式
import * as ElementPlusIconsVue from '@element-plus/icons-vue' //导入 ElementPlus 组件库中的所有图标
import zhCn from 'element-plus/dist/locale/zh-cn.mjs' //导入 ElementPlus 组件库的中文语言包

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
app.mount('#app')
```

按需导入
src/main.js
```src/main.js
import { createApp } from 'vue'

/*
//整体导入 ElementPlus 组件库
import ElementPlus from 'element-plus' //导入 ElementPlus 组件库的所有模块和功能 
import 'element-plus/dist/index.css' //导入 ElementPlus 组件库所需的全局 CSS 样式
import * as ElementPlusIconsVue from '@element-plus/icons-vue' //导入 ElementPlus 组件库中的所有图标
import zhCn from 'element-plus/dist/locale/zh-cn.mjs' //导入 ElementPlus 组件库的中文语言包
*/

import App from './App.vue'

const app = createApp(App)

/*
//注册 ElementPlus 组件库中的所有图标到全局 Vue 应用中
for (const [key, component] of Object.entries(ElementPlusIconsVue)) {
    app.component(key, component)
}
//app.use(ElementPlus) //将 ElementPlus 插件注册到 Vue 应用中s
app.use(ElementPlus, {
    locale: zhCn // 设置 ElementPlus 组件库的区域语言为中文简体
})
*/

app.mount('#app')
```

按需导入
vite.config.js
```vite.config.js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

//unplugin
import AutoImport from 'unplugin-auto-import/vite'
import Components from 'unplugin-vue-components/vite'
import { ElementPlusResolver } from 'unplugin-vue-components/resolvers'
import Icons from 'unplugin-icons/vite' //图标
import IconsResolver from 'unplugin-icons/resolver'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    AutoImport({
      // 自动导入 Vue 相关函数，如：ref, reactive, toRef 等
      imports: ['vue'],
      resolvers: [
        ElementPlusResolver(),
        // 自动导入图标组件
        IconsResolver(),
      ],
    }),
    Components({
      resolvers: [
        ElementPlusResolver(),
        // 自动注册图标组件
        IconsResolver({
          enabledCollections: ['ep'],
        }),
      ],
    }),
    Icons({
      autoInstall: true,
    }),
  ],
})
```

## 1. 按钮

src/App.vue
```src/App.vue
<script setup></script>

<template>
	<h3>按钮</h3>
	<el-button>默认按钮</el-button>
	<el-button type="primary">主要按钮</el-button>
	<el-button type="success">成功按钮</el-button>
	<el-button type="info">信息按钮</el-button>
	<el-button type="warning">警告按钮</el-button>
	<el-button type="danger">危险按钮</el-button>

	<hr>
	<h3>按钮属性</h3>
	<el-button plain>朴素按钮</el-button>
	<el-button round>圆角按钮</el-button>
	<el-button circle>圆</el-button>
	<el-button disabled>禁用按钮</el-button>
	<el-button loading>加载中</el-button>

	<hr>
	<h3>尺寸</h3>
	<el-button size="large">大型按钮</el-button>
	<el-button>默认按钮</el-button>
	<el-button size="small">小型按钮</el-button>
</template>

<style scoped></style>
```

## 2. 图标

App.vue
```App.vue
<script setup></script>

<template>
	<h3>图标</h3><el-icon><Plus /></el-icon>
	<el-icon><Edit /></el-icon>
	<el-icon><Delete /></el-icon>
	<el-icon class="is-loading"><Loading /></el-icon>

	<hr>
	<h3>属性</h3>
	<el-icon size="30" color="red"><Search /></el-icon>

	<hr>
	<h3>按钮</h3>
	<el-button type="primary">
		<el-icon><Search /></el-icon>
		<span> 搜索 </span>
	</el-button>

	<el-button type="primary">
		<el-icon><Search /></el-icon>
	</el-button>

	<el-button type="primary" circle>
		<el-icon><Search /></el-icon>
	</el-button>

	<hr>
	<h3>按钮组</h3>
	<el-button-group>
		<el-button type="primary">
			<el-icon><Plus /></el-icon>
		</el-button>

		<el-button type="primary">
			<el-icon><Edit /></el-icon>
		</el-button>

		<el-button type="primary">
			<el-icon><Delete /></el-icon>
		</el-button>
	</el-button-group>
</template>

<style scoped></style>
```

## 3. 提示框
App.vue
```App.vue
<script setup>
import { ElMessage, ElMessageBox, ElNotification } from 'element-plus'

// 消息
const openMsg = () => {
	ElMessage({
		type: 'success', // success | warning | info | error
		message: 'dengruicode.com',
		showClose: true
	})
}

// 确认框
const openConfirm = () => {
	ElMessageBox.confirm('确认删除?', '标题', {
		type: 'warning',
		confirmButtonText: '确认',
		cancelButtonText: '取消'
	}).then(() => {
		console.log('确认')
	}).catch(() => {
		console.log('取消')
	})
}

// 通知
const openNotify = () => {
	ElNotification({
		title: '标题',
		message: '邓瑞编程',
		duration: 1500 // 展示时间 [单位:毫秒]
	})
}

// 通知2
const openNotify2 = () => {
	ElNotification({
		type: 'success', // success | warning | info | error
		title: '标题',
		message: 'dengruicode.com',
		duration: 1500,
		position: 'bottom-right'
	})
}
</script>

<template>
	<el-button @click="openMsg">消息</el-button>
	<el-button @click="openConfirm">确认框</el-button>
	<el-button @click="openNotify">通知</el-button>
	<el-button @click="openNotify2">通知2</el-button>
</template>

<style scoped></style>
```

## 4. 导航
App.vue
```App.vue
```

## 5. 标签页
App.vue
```App.vue
```

## 6. 输入框
App.vue
```App.vue
```

## 7. 单选框、复选框
App.vue
```App.vue
```

## 8. 下拉框
App.vue
```App.vue
```

## 9. 日期选择器
App.vue
```App.vue
```

## 10. 表单
App.vue
```App.vue
```

## 11. 对话框
App.vue
```App.vue
```

## 12. 分页
App.vue
```App.vue
```

## 13. 表格
App.vue
```App.vue
```