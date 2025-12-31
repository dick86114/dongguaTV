# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

E视界 Android 客户端 - 基于 Capacitor 框架的 Android 应用打包项目。将 Web 应用打包为可在 Android 设备上安装的原生 APK。

**技术栈:**
- **框架**: Capacitor 8.0
- **前端**: Vue 3 + Bootstrap 5 (在 public/ 目录)
- **构建**: Gradle 8.x
- **自动化**: GitHub Actions

## 快速开始

### 安装依赖
```bash
# 前端项目使用 pnpm 替代 npm
pnpm install

# 或使用 npm
npm install
```

### 配置应用

编辑 [capacitor.config.json](capacitor.config.json):

```json
{
  "appId": "com.ednovas.donguatv",
  "appName": "E视界",
  "webDir": "public",
  "server": {
    "url": "https://your-server.com",
    "cleartext": true
  }
}
```

**重要配置:**
- `server.url`: Web 服务器地址 (支持 HTTP/HTTPS)
- `server.cleartext`: 是否允许 HTTP 访问 (内网使用需设为 true)
- `appId`: 应用唯一标识符 (反向域名格式)

### 开发命令

```bash
# 同步 Web 资源到 Android
npm run sync
# 或
npx cap sync android

# 打开 Android Studio
npm run open

# 构建 Release APK
npm run build

# 清理 Android 构建缓存
cd android && ./gradlew clean

# 安装到连接的设备
cd android && ./gradlew installDebug
```

## 项目架构

### 目录结构

```
dongguaTV/
├── public/                      # Web 资源目录
│   ├── index.html              # Web 应用入口 (Vue 3 SPA)
│   ├── icon.png                # 应用图标源文件 (1024x1024)
│   ├── libs/                   # 本地依赖库
│   │   ├── js/                 # JavaScript 库
│   │   │   ├── vue.global.prod.min.js
│   │   │   ├── bootstrap.bundle.min.js
│   │   │   ├── hls.min.js
│   │   │   └── DPlayer.min.js
│   │   └── css/                # 样式库
│   ├── manifest.json           # PWA 配置
│   └── sw.js                   # Service Worker
├── android/                     # Android 原生项目
│   ├── app/                    # 应用模块
│   │   └── src/main/           # 主要源代码
│   │       ├── res/            # 资源文件 (图标、布局等)
│   │       └── AndroidManifest.xml
│   ├── gradle/                 # Gradle 配置
│   ├── build.gradle            # 项目级构建配置
│   └── gradlew                 # Gradle 包装脚本
├── capacitor.config.json        # Capacitor 配置文件
├── package.json                 # Node 依赖配置
├── .github/workflows/           # GitHub Actions 工作流
│   └── android-build.yml       # 自动构建工作流
├── .gitignore                  # Git 忽略配置
└── README.md                   # 项目文档
```

### 核心文件说明

**[capacitor.config.json](capacitor.config.json)** - Capacitor 配置
- 应用标识符和名称
- Web 资源目录路径
- 服务器 URL (HTTP/HTTPS)
- 插件配置 (状态栏等)

**[.github/workflows/android-build.yml](.github/workflows/android-build.yml)** - 自动构建工作流
- 支持两种触发方式: Tag 推送 / 手动触发
- 自动生成多分辨率应用图标
- 自动处理 APK 签名 (优先使用 Secrets, 否则用调试密钥)
- 自动发布 GitHub Release

**[public/index.html](public/index.html)** - Web 应用入口
- Vue 3 单页应用
- Bootstrap 5 UI 框架
- DPlayer 视频播放器
- HLS.js 流媒体支持

**[package.json](package.json)** - 依赖配置
仅包含 Capacitor 相关依赖:
- `@capacitor/android` - Android 平台支持
- `@capacitor/core` - 核心 API
- `@capacitor/status-bar` - 状态栏插件
- `@capacitor/cli` - 命令行工具

## GitHub Actions 自动构建

### 工作流特性

**1. 自定义图标支持**
- **手动触发时可指定图标 URL**: 无需修改代码即可更换应用图标
- 支持任何公开可访问的图片 URL (图床、GitHub raw 链接等)
- 自动下载并生成所有分辨率的 Android 图标
- 未指定时使用 [public/icon.png](public/icon.png) 作为默认图标

**2. 自动图标生成**
- 从源图标自动生成所有分辨率的 Android 图标
- 支持 mdpi ~ xxxhdpi (36x36 ~ 432x432)
- 自动添加安全边距,防止圆形遮罩裁剪

**3. 自动签名**
- **优先**: GitHub Secrets 中的正式签名密钥
- **备用**: 自动生成调试密钥签名

**4. 多架构支持**
- `armeabi-v7a` (ARM 32位)
- `arm64-v8a` (ARM 64位)
- `x86` / `x86_64` (模拟器)

### 触发方式

**方式一: Tag 触发**
```bash
git tag v1.0.0
git push origin v1.0.0
```

**方式二: 手动触发 (支持自定义图标)**
1. GitHub 仓库 → Actions → Android Build & Release
2. Run workflow → 填写参数:
   - Server URL: 服务器地址 (支持 HTTP/HTTPS)
   - App Name: 应用名称
   - Version Tag: 版本号
   - **自定义应用图标 URL**: (可选) 图标的公开访问 URL
     - 留空: 使用项目默认图标 [public/icon.png](public/icon.png)
     - 填写 URL: 自动下载并使用该图标
     - 示例: `https://raw.githubusercontent.com/user/repo/main/icon.png`

**图标 URL 获取方式:**

1. **GitHub Raw 链接** (推荐):
   ```
   https://raw.githubusercontent.com/用户名/仓库名/分支名/icon.png
   ```

2. **图床服务**:
   ```
   https://imgur.com/xxx.png (或其他图床)
   ```

3. **临时文件服务** (测试用):
   ```
   https://temp.sh/xxx.png (注意可能过期)
   ```

### 配置正式签名密钥 (可选)

在 GitHub Secrets 中添加:
- `SIGNING_KEY`: Base64 编码的 keystore 文件
- `KEY_STORE_PASSWORD`: Keystore 密码
- `ALIAS`: 密钥别名
- `KEY_PASSWORD`: 密钥密码

**生成 Base64 编码:**
```bash
# Linux/Mac
base64 -w 0 release.keystore

# Windows PowerShell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("release.keystore"))
```

## HTTP 访问配置

> ⚠️ 重要: Android 9+ 默认禁止 HTTP 明文流量

本项目已内置 HTTP 支持:

1. **[capacitor.config.json](capacitor.config.json)**: `"cleartext": true`
2. **Android 项目**: 已配置 `android:usesCleartextTraffic="true"`

**使用 HTTP 地址构建:**
- 本地构建: 直接在配置文件中使用 HTTP URL
- GitHub Actions: 手动触发时填写 HTTP 地址

## 常见任务

### 修改应用配置

1. 编辑 [capacitor.config.json](capacitor.config.json)
2. 修改 `appName`、`server.url` 等
3. 执行 `npm run sync` 同步到 Android

### 更换应用图标

**方式一: 使用 GitHub Actions (推荐,无需修改代码)**

1. 准备图标文件:
   - 格式: PNG 或 JPG
   - 尺寸: 推荐 512x512 或更大
   - 上传到公开可访问的位置 (图床/GitHub 仓库)

2. 获取图标 URL:
   ```bash
   # GitHub Raw 链接示例
   https://raw.githubusercontent.com/yourname/your-repo/main/my-icon.png
   ```

3. 触发构建:
   - GitHub → Actions → Android Build & Release → Run workflow
   - 填写 **自定义应用图标 URL** 参数
   - 构建完成即可使用新图标

**方式二: 修改项目默认图标**

1. 替换 [public/icon.png](public/icon.png) (推荐 1024x1024 PNG)
2. 提交到 Git 仓库: `git add public/icon.png && git commit -m "更新应用图标"`
3. GitHub Actions 会自动使用新图标构建

**方式三: 本地构建**

1. 替换 [public/icon.png](public/icon.png)
2. 执行 `npm run sync` 更新图标
3. 构建 APK: `npm run build`

### 本地构建调试版本

```bash
# 同步资源
npm run sync

# 构建 Debug APK
cd android && ./gradlew assembleDebug

# APK 位置
# android/app/build/outputs/apk/debug/app-debug.apk
```

### 本地构建发布版本

```bash
# 同步资源
npm run sync

# 构建 Release APK
cd android && ./gradlew assembleRelease

# APK 位置
# android/app/build/outputs/apk/release/app-release-unsigned.apk

# 需要签名后才能安装
```

### 安装到连接的设备

```bash
# 确保 USB 调试已开启
cd android && ./gradlew installDebug
```

### 清理构建缓存

```bash
# Android 项目清理
cd android && ./gradlew clean

# 清理并重新构建
cd android && ./gradlew clean assembleRelease
```

## 技术细节

### Capacitor 工作原理

```
Web 资源 (public/)
        ↓
    cap sync
        ↓
Android WebView (android/app/src/main/assets/public/)
        ↓
    Capacitor 桥接
        ↓
原生功能 (状态栏、文件系统等)
```

### 应用图标生成

GitHub Actions 使用 ImageMagick 自动生成:

```
public/icon.png (1024x1024)
        ↓
   ImageMagick
        ↓
android/app/src/main/res/mipmap-*/ic_launcher.png
android/app/src/main/res/mipmap-*/ic_launcher_round.png
android/app/src/main/res/mipmap-*/ic_launcher_foreground.png
```

支持的分辨率:
- mdpi: 48x48 (36x36 图标)
- hdpi: 72x72 (54x54 图标)
- xhdpi: 96x96 (72x72 图标)
- xxhdpi: 144x144 (108x108 图标)
- xxxhdpi: 192x192 (144x144 图标)

### APK 签名流程

```
unsigned.apk
    ↓
  Zipalign
    ↓
aligned.apk
    ↓
  Apksigner (使用 keystore)
    ↓
signed.apk (可安装)
```

## 重要注意事项

### 依赖管理
- 只使用 Capacitor 官方包
- 不需要安装 Express、Axios 等后端依赖
- 前端库已本地化在 `public/libs/` 目录

### 配置修改
- 修改配置后**必须执行** `npm run sync`
- Capacitor 不自动监听配置变化
- 同步后建议重新构建

### HTTP 访问
- 内网环境 (http://192.168.x.x) 需要设置 `"cleartext": true`
- 生产环境建议使用 HTTPS
- Android 项目已内置 Network Security Policy

### 版本管理
- 使用 Git Tag 触发自动构建 (推荐)
- 手动触发可自定义配置
- 版本号格式: `v1.0.0`

### 构建时间
- 首次 GitHub Actions 构建: 10-20 分钟
- 后续构建会快很多 (使用缓存)
- 本地构建: 2-5 分钟

## 开发规范

### 代码风格
- JSON 配置文件使用 2 空格缩进
- 注释使用中文
- 遵循 Capacitor 最佳实践

### 提交规范
- 修改配置后提交说明应包含修改内容
- Tag 推送会触发自动构建
- 避免在主分支直接修改

### 安全考虑
- 签名密钥不要提交到仓库
- 使用 GitHub Secrets 存储敏感信息
- 生产环境使用正式签名密钥

## 相关资源

### 官方文档
- [Capacitor 文档](https://capacitorjs.com/)
- [Android 开发文档](https://developer.android.com/)
- [Gradle 文档](https://docs.gradle.org/)

### 项目文件
- [capacitor.config.json](capacitor.config.json) - 应用配置
- [.github/workflows/android-build.yml](.github/workflows/android-build.yml) - 自动构建工作流
- [package.json](package.json) - 依赖配置

### 免责声明
本项目仅供学习交流使用。
