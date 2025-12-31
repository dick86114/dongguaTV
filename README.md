# E视界 Android 客户端

这是 E视界 (DongguaTV) 项目的 Android 客户端,基于 Capacitor 框架构建,可将 Web 应用打包为原生 Android APK。

## 项目说明

本项目仅用于构建 Android 客户端,将 Web 应用打包为可在 Android 设备上安装的 APK 文件。

## 功能特性

- 🎬 支持手机、平板、Android TV/电视盒子
- 📺 完美适配电视遥控器操作
- 🌙 沉浸式状态栏支持 (Immersive Status Bar)
- 🔒 支持 HTTP/HTTPS 双协议访问
- 🎨 自动生成多分辨率应用图标

## 快速开始

### 前置要求

- Node.js v18+
- npm 或 pnpm

### 安装依赖

```bash
# 使用 pnpm (推荐)
pnpm install

# 或使用 npm
npm install
```

### 配置应用

编辑 `capacitor.config.json` 文件:

```json
{
  "appId": "com.ednovas.donguatv",
  "appName": "E视界",
  "webDir": "public",
  "server": {
    "url": "https://your-server.com",  // 修改为你的服务器地址
    "cleartext": true                   // 允许 HTTP 访问
  },
  "plugins": {
    "StatusBar": {
      "overlaysWebView": false,
      "style": "DARK"
    }
  }
}
```

**重要配置说明:**

| 配置项 | 说明 |
|--------|------|
| `appId` | 应用唯一标识符 (反向域名格式) |
| `appName` | 应用显示名称 |
| `server.url` | Web 服务器地址 (支持 HTTP/HTTPS) |
| `server.cleartext` | 是否允许 HTTP 明文访问 (内网使用需要设为 true) |

### 同步资源

```bash
npm run sync
# 或
npx cap sync android
```

此命令会:
- 将 `public/` 目录的 Web 资源复制到 Android 项目
- 生成 Android 所需的配置文件
- 更新应用图标和元数据

### 本地构建

#### 方式一: 命令行构建

```bash
npm run build
```

构建完成后,APK 文件位于:
```
android/app/build/outputs/apk/release/app-release.apk
```

#### 方式二: 使用 Android Studio

```bash
# 打开 Android Studio
npm run open
# 或
npx cap open android
```

然后在 Android Studio 中:
1. 选择 `Build` → `Generate Signed Bundle / APK`
2. 选择 `APK`
3. 配置签名密钥 (首次可用调试密钥)
4. 选择 `release` 构建类型
5. 点击 `Finish`

## GitHub Actions 自动构建

本项目配置了自动化构建流程,支持两种触发方式:

### 方式一: Tag 触发 (推荐)

推送版本标签时自动构建:

```bash
git tag v1.0.0
git push origin v1.0.0
```

### 方式二: 手动触发

1. 进入 GitHub 仓库的 **Actions** 页面
2. 选择 **Android Build & Release** 工作流
3. 点击 **Run workflow**
4. 填写配置信息:
   - **Server URL**: 你的服务器地址 (如 `https://example.com` 或 `http://192.168.1.100:3000`)
   - **App Name**: 应用名称 (如 `我的影院`)
   - **Version Tag**: 版本号 (如 `v1.0.0`)
   - **自定义应用图标 URL**: (可选) 图标的公开访问 URL,留空则使用默认图标
5. 点击 **Run workflow** 开始构建

**自定义图标说明:**

如果你想在构建时使用自己的应用图标,可以提供图标的 URL。工作流会自动下载并生成所有分辨率的图标。

**图标要求:**
- 格式: PNG (推荐) 或 JPG
- 尺寸: 推荐 512x512 或更大 (正方形)
- 背景: 透明或纯色背景均可
- URL 必须是公开可访问的 (支持重定向)

**如何获取图标 URL:**

方式一 - 使用图床服务 (推荐):
```bash
# 将图标上传到图床,获取公开访问链接
# 例如: https://example.com/my-icon.png
```

方式二 - 使用 GitHub 仓库:
```bash
# 1. 将图标上传到你的 GitHub 仓库
# 2. 获取 raw 链接
# 格式: https://raw.githubusercontent.com/用户名/仓库名/分支名/icon.png
# 例如: https://raw.githubusercontent.com/yourname/repo/main/icon.png
```

方式三 - 使用临时文件服务 (测试用):
```bash
# 使用免费临时文件分享服务
# 例如: https://temp.sh / https://file.io 等
# 注意: 临时链接可能会过期
```

**示例配置:**
```
Server URL: https://my-movie-site.com
App Name: 我的影院
Version Tag: v1.0.0
自定义应用图标 URL: https://raw.githubusercontent.com/yourname/icons/main/my-icon.png
```

### 下载 APK

构建完成后:
1. 进入 GitHub 仓库的 **Releases** 页面
2. 找到对应的版本
3. 下载 `E视界-x.x.x-universal.apk` 文件

### 自动签名

工作流会自动处理 APK 签名:

- **优先使用**: GitHub Secrets 中配置的正式签名密钥
- **备用方案**: 自动生成调试密钥进行签名

**配置正式签名密钥 (可选):**

在 GitHub 仓库设置中添加以下 Secrets:
- `SIGNING_KEY`: Base64 编码的 keystore 文件内容
- `KEY_STORE_PASSWORD`: Keystore 密码
- `ALIAS`: 密钥别名
- `KEY_PASSWORD`: 密钥密码

**生成 Base64 编码的 keystore:**

```bash
base64 -w 0 release.keystore
# Windows PowerShell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("release.keystore"))
```

## 应用图标

本项目支持三种方式设置应用图标:

### 方式一: 使用 GitHub Actions 自定义图标 (推荐)

在手动触发构建时,填写 **自定义应用图标 URL** 参数:
- 支持任何公开可访问的图片 URL
- 自动下载并生成所有分辨率
- 无需修改代码即可更换图标

### 方式二: 修改项目默认图标

替换 [public/icon.png](public/icon.png) 文件:
- 推荐尺寸: 1024x1024 像素
- 格式: PNG (透明背景效果最佳)
- GitHub Actions 会自动生成所有分辨率 (mdpi ~ xxxhdpi)

### 方式三: 本地构建时使用自定义图标

```bash
# 1. 替换 public/icon.png
cp /path/to/your/icon.png public/icon.png

# 2. 同步资源
npm run sync

# 3. 构建
npm run build
```

**图标规格:**
- 推荐尺寸: 1024x1024 或 512x512 像素
- 格式: PNG (推荐透明背景) 或 JPG
- 会自动生成所有所需分辨率 (mdpi ~ xxxhdpi)
  - mdpi: 48x48 (36x36 图标)
  - hdpi: 72x72 (54x54 图标)
  - xhdpi: 96x96 (72x72 图标)
  - xxhdpi: 144x144 (108x108 图标)
  - xxxhdpi: 192x192 (144x144 图标)

## HTTP 访问配置

> ⚠️ 重要: Android 9+ 默认禁止明文 HTTP 流量

本项目已内置 HTTP 支持。如果你的服务器使用 HTTP 协议 (如 `http://192.168.1.100:3000`),请确保:

1. **capacitor.config.json** 中设置 `"cleartext": true`
2. **Android 项目已配置** Network Security Policy (已包含)

### 使用 GitHub Actions 构建 HTTP 版本

在手动触发构建时,直接填写 HTTP 地址即可:
```
Server URL: http://192.168.1.100:3000
```

## 项目结构

```
dongguaTV/
├── public/                   # Web 资源目录
│   ├── index.html           # Web 应用入口
│   ├── icon.png             # 应用图标 (源)
│   ├── libs/                # 本地依赖库
│   ├── manifest.json        # PWA 配置
│   └── sw.js                # Service Worker
├── android/                  # Android 原生项目
│   ├── app/                 # 应用模块
│   ├── gradle/              # Gradle 配置
│   └── build.gradle         # 构建配置
├── capacitor.config.json     # Capacitor 配置
├── package.json              # Node 依赖配置
└── .github/workflows/        # GitHub Actions 工作流
    └── android-build.yml    # 自动构建工作流
```

## 常见问题

### 1. 构建失败: gradlew 权限错误

```bash
chmod +x android/gradlew
```

### 2. 无法连接 HTTP 服务器

确认以下配置:
- `capacitor.config.json` 中 `"cleartext": true`
- 重新执行 `npx cap sync android`
- 重新构建 APK

### 3. GitHub Actions 构建很慢

正常现象,首次构建可能需要 10-20 分钟。
- 下载依赖: ~5 分钟
- 构建 Android: ~10 分钟
- 后续构建会快很多 (使用缓存)

### 4. APK 安装后无法打开

检查:
- Android 版本是否 ≥ 5.0 (API Level 21)
- 是否允许安装"未知来源应用"
- 下载完整的 APK (不要使用部分下载)

### 5. 电视遥控器无法操作

确认:
- 设备为 Android TV 或已安装 Leanback Launcher
- 使用正确的 APK (universal 版本包含 TV 支持)

## 开发命令

```bash
# 安装依赖
pnpm install

# 同步资源到 Android
npm run sync

# 打开 Android Studio
npm run open

# 命令行构建 Release APK
npm run build

# 清理 Android 构建缓存
cd android && ./gradlew clean

# 安装到连接的设备
cd android && ./gradlew installDebug
```

## 技术栈

- **框架**: Capacitor 8.0
- **前端**: Vue 3 + Bootstrap 5
- **构建工具**: Gradle 8.x
- **最低 SDK**: API 21 (Android 5.0)
- **目标 SDK**: API 34 (Android 14)

## 更新日志

### v1.0.0
- 初始版本
- 支持手机/平板/Android TV
- 沉浸式状态栏支持
- HTTP/HTTPS 双协议支持
- 自动图标生成
- GitHub Actions 自动构建

## 许可证

本项目仅供学习交流使用。

## 相关链接

- [Capacitor 官方文档](https://capacitorjs.com/)
- [Android 开发文档](https://developer.android.com/)
- E视界 Web 项目: https://github.com/ednovas/dongguaTV
