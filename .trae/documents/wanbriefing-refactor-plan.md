# 湾水告示栏改造计划

## 概述

对 fork 的湾水告示栏项目进行三项改造：字体替换、音乐播放、内容管理后台，完成后推送到 GitHub。

## 当前状态分析

- **项目**：纯静态单页面站点（湾水告示栏），托管在 GitHub Pages
- **技术栈**：HTML5 + CSS3 + 原生 JavaScript，无任何外部依赖
- **文件结构**：仅一个 `index.html` + 图片资源
- **当前字体**：`font-family: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif`
- **无现有音频功能**
- **无现有后台管理功能**
- **Git 远程**：`ixix1011/1011-wanbriefing`（用户 fork），需同时推送到 `Fennmoo/wanbriefing`

---

## 改动 1：字体改为苹方 + San Francisco

### 方案

苹方（PingFang SC）和 San Francisco 是 Apple 系统字体，仅 macOS/iOS 原生可用，Apple 许可协议不允许作为 Web Font 托管。

**策略**：
- 优先使用 Apple 系统字体
- 非 Apple 设备使用高质量开源替代字体（Noto Sans SC 作为中文回退，Inter 作为英文回退）
- 通过 Google Fonts CDN 加载回退字体，确保跨平台一致性

### 具体修改

**文件**：`index.html`

1. 在 `<head>` 中添加 Google Fonts 引用：
   ```html
   <link rel="preconnect" href="https://fonts.googleapis.com">
   <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
   <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Noto+Sans+SC:wght@400;500;600;700&display=swap" rel="stylesheet">
   ```

2. 修改 `body` 的 `font-family`：
   ```css
   font-family: -apple-system, 'SF Pro Display', 'SF Pro Text', 'PingFang SC', 'Inter', 'Noto Sans SC', 'Helvetica Neue', 'Microsoft YaHei', sans-serif;
   ```

3. 对英文标题/导航等元素单独设置英文字体优先级，中文内容区域设置中文字体优先级

---

## 改动 2：浮动迷你音乐播放器

### 方案

在页面底部添加一个浮动迷你播放器，用户可上传本地音频文件播放。设计风格与现有毛玻璃效果一致（一体成型）。

### 功能

- 上传本地音频文件（mp3, wav, ogg, flac 等）
- 播放/暂停
- 进度条（可拖拽跳转）
- 音量控制
- 上一首/下一首（多文件播放列表）
- 当前曲目名称显示
- 最小化/展开切换

### 具体修改

**文件**：`index.html`

1. 在 `<style>` 中添加播放器样式：
   - 固定定位在页面底部
   - 毛玻璃背景效果（与现有导航栏风格一致）
   - 圆角、阴影、配色与现有设计统一
   - 响应式适配移动端

2. 在 `<body>` 底部（footer 之后）添加播放器 HTML 结构：
   - 迷你模式：只显示播放/暂停按钮 + 曲目名
   - 展开模式：完整控件（进度条、音量、播放列表）
   - 文件上传区域

3. 在 `<script>` 中添加播放器逻辑：
   - 使用 HTML5 Audio API
   - File API 读取用户上传的音频文件
   - 播放列表管理
   - 进度条拖拽交互
   - localStorage 保存播放器状态（音量等偏好）

---

## 改动 3：内容管理后台

### 方案

创建独立的 `admin.html` 页面，通过网页链接访问。使用 localStorage 存储内容配置，`index.html` 优先读取 localStorage 中的内容，不存在时回退到默认硬编码内容。

### 功能

- 编辑公告文本内容（地铁告示栏、公交告示栏）
- 修改更新日期
- 配置轮播图片（通过输入图片 URL）
- 修改欢迎标题
- 预览修改效果
- 一键重置为默认内容
- 简单的密码保护（防止随意修改）

### 具体修改

**新建文件**：`admin.html`

1. 独立的管理页面，与主站风格一致
2. 表单界面，包含所有可编辑字段
3. 保存到 localStorage
4. 预览功能（在新标签页打开 index.html）

**修改文件**：`index.html`

1. 页面加载时检查 localStorage 中是否有自定义内容
2. 如果有，用 localStorage 中的内容替换默认内容
3. 如果没有，保持现有硬编码内容不变

### 数据结构

```json
{
  "welcomeTitle": "欢迎来到湾水告示栏",
  "welcomeTitleEn": "Welcome to the Wanshui Briefing",
  "subwayDate": "2026/5/10",
  "subwayText": "公告内容...",
  "busDate": "2026/5/10",
  "busText": "公告内容...",
  "bannerImages": ["url1", "url2", "url3"],
  "navLinks": [
    {"label": "地铁告示栏", "href": "#subway"},
    {"label": "公交告示栏", "href": "#bus"}
  ]
}
```

---

## 改动 4：推送到 GitHub

### 步骤

1. 提交所有改动到当前 fork：`ixix1011/1011-wanbriefing`
2. 添加原始仓库为第二个远程
3. 推送到原始仓库：`Fennmoo/wanbriefing`（需要 push 权限或通过 PR）

### 注意事项

- 推送到 `Fennmoo/wanbriefing` 需要该仓库的写权限，如果没有则需要通过 Pull Request 方式
- 建议先推送到自己的 fork，确认无误后再处理原始仓库

---

## 假设与决策

| 决策项 | 选择 | 原因 |
|--------|------|------|
| 字体回退方案 | Google Fonts (Inter + Noto Sans SC) | Apple 字体不可作为 Web Font，需跨平台回退 |
| 音乐播放器位置 | 页面底部固定浮动 | 不遮挡主内容，符合常见播放器习惯 |
| 音乐文件来源 | 用户上传本地文件 | 用户明确选择此方式 |
| 后台数据存储 | localStorage | 纯静态站点无服务器，最简方案 |
| 后台访问方式 | 独立 admin.html 页面 | 通过 URL 链接访问，与主站分离 |
| 后台安全 | 简单密码保护 | 防止随意修改，非企业级安全需求 |

---

## 验证步骤

1. **字体验证**：在浏览器中打开 index.html，通过 DevTools 检查 computed font-family，确认 Apple 设备使用苹方/SF，非 Apple 设备使用 Inter/Noto Sans SC
2. **播放器验证**：上传多个音频文件，测试播放/暂停/切歌/进度拖拽/音量调节/最小化展开
3. **后台验证**：在 admin.html 修改内容并保存，刷新 index.html 确认内容已更新；测试重置功能
4. **响应式验证**：在移动端视口下检查所有新功能是否正常显示
5. **Git 验证**：确认代码已成功推送到两个远程仓库
