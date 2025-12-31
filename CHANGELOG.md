# 更新日志

## [2025-12-31] 项目精简 + 自定义图标功能

### ✨ 新增功能

**GitHub Actions 自定义图标支持**
- 在手动触发构建时可以指定自定义应用图标 URL
- 支持任何公开可访问的图片 URL (图床、GitHub raw 链接等)
- 自动下载并生成所有分辨率的 Android 图标
- 无需修改代码即可更换应用图标
- 新增工作流参数: `custom_icon_url`

**新增文档**
- [CUSTOM_ICON_GUIDE.md](CUSTOM_ICON_GUIDE.md) - 自定义图标使用指南
- [CLAUDE.md](CLAUDE.md) - Claude Code 开发指南
- 更新 [README.md](README.md) - 完善使用文档

### 🔧 优化改进

**项目精简**
- ✅ 删除所有后端服务器相关代码 (server.js, api/)
- ✅ 删除部署配置文件 (Dockerfile, vercel.json, install.sh)
- ✅ 删除代理脚本 (cloudflare-*-proxy.js, proxy-server.js)
- ✅ 清理 package.json,仅保留 Capacitor 相关依赖
- ✅ 专注于 Android 客户端构建功能

**依赖清理**
[package.json](package.json) 精简为:
```json
{
  "name": "dongguatv-android",
  "version": "1.0.0",
  "description": "E视界 Android 客户端",
  "scripts": {
    "sync": "npx cap sync android",
    "open": "npx cap open android",
    "build": "npm run sync && cd android && ./gradlew assembleRelease"
  },
  "dependencies": {
    "@capacitor/android": "^8.0.0",
    "@capacitor/core": "^8.0.0",
    "@capacitor/status-bar": "^8.0.0"
  },
  "devDependencies": {
    "@capacitor/cli": "^8.0.0"
  }
}
```

**工作流优化**
[.github/workflows/android-build.yml](.github/workflows/android-build.yml):
- 新增: 下载自定义图标步骤
- 改进: 图标生成步骤增加错误检查
- 改进: 显示图标信息用于调试

### 📝 文档更新

**[README.md](README.md)**
- 更新为 Android 项目专用文档
- 删除所有后端和部署相关内容
- 新增自定义图标使用说明
- 新增图标 URL 获取方式示例
- 新增三种图标设置方式说明

**[CLAUDE.md](CLAUDE.md)**
- 完整的 Android 开发指南
- GitHub Actions 工作流详解
- 自定义图标功能说明
- 常见任务和故障排查

**[CUSTOM_ICON_GUIDE.md](CUSTOM_ICON_GUIDE.md)** (新增)
- 详细的自定义图标使用指南
- 图标准备和上传步骤
- URL 获取方式 (GitHub/图床/临时服务)
- 示例场景和高级技巧
- 故障排查和最佳实践

### 🗑️ 删除文件

**后端服务器**
- server.js (1619 行)
- api/index.js (371 行)

**部署配置**
- Dockerfile
- vercel.json
- .github/workflows/docker-publish.yml
- install.sh (341 行)

**代理脚本**
- cloudflare-cors-proxy.js
- cloudflare-tmdb-proxy.js
- proxy-server.js

**配置文件**
- .dockerignore
- .env.example
- db.template.json

### 📊 代码统计

```
15 files changed, 298 insertions(+), 3891 deletions(-)
```

删除了约 3900 行代码,新增约 300 行文档和配置。

### 🎯 项目定位

**之前**: 全栈流媒体聚合播放器 (前端 + 后端 + Android)

**现在**: 纯 Android 客户端构建项目

专注于:
- 将 Web 应用打包为 Android APK
- GitHub Actions 自动化构建
- 自定义应用图标和配置
- 多架构支持 (ARM/x86)

### 🚀 使用方法

**本地构建**:
```bash
pnpm install
npm run build
```

**GitHub Actions 构建**:
1. Actions → Android Build & Release → Run workflow
2. 填写:
   - Server URL: 你的服务器地址
   - App Name: 应用名称
   - Version Tag: 版本号
   - **自定义应用图标 URL**: (可选) 图标 URL
3. Run workflow

### ✅ 测试建议

1. **测试自定义图标功能**:
   - 准备一个测试图标 URL
   - 手动触发构建并填写图标 URL
   - 检查构建日志中的图标下载信息
   - 下载 APK 并验证图标显示

2. **测试默认图标**:
   - 手动触发构建,留空图标 URL
   - 确认使用 [public/icon.png](public/icon.png)
   - 验证图标正常生成

3. **测试 Tag 触发**:
   ```bash
   git tag v1.0.0-test
   git push origin v1.0.0-test
   ```
   - 确认自动构建成功
   - 确认使用默认图标

### 📚 相关文档

- [README.md](README.md) - 项目文档
- [CLAUDE.md](CLAUDE.md) - 开发指南
- [CUSTOM_ICON_GUIDE.md](CUSTOM_ICON_GUIDE.md) - 自定义图标指南
- [.github/workflows/android-build.yml](.github/workflows/android-build.yml) - 构建工作流

### 🐛 已知问题

无

### 🔮 后续计划

- [ ] 添加自定义应用启动图标功能
- [ ] 支持自定义应用主题色
- [ ] 添加构建参数验证
- [ ] 支持批量构建多个版本

---

## 版本历史

### v1.0.0 (初始版本)
- 基于 Capacitor 的 Android 客户端
- GitHub Actions 自动构建
- 支持手机/平板/Android TV
- HTTP/HTTPS 双协议支持

### v1.1.0 (当前版本)
- ✨ 新增自定义图标功能
- 🔧 项目精简,专注 Android 构建
- 📝 完善文档和使用指南
