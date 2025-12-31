# 自定义应用图标使用指南

本文档说明如何在 GitHub Actions 构建时使用自定义应用图标。

## 快速开始

### 步骤 1: 准备图标文件

**图标要求:**
- 格式: PNG (推荐) 或 JPG
- 尺寸: 推荐 512x512 或 1024x1024 像素 (正方形)
- 背景: 透明或纯色背景均可
- 文件大小: 建议小于 1MB

**图标设计建议:**
- 使用简洁的设计,小尺寸下也能清晰识别
- 避免过多细节,会被缩小到 36x36 像素
- 推荐使用品牌色或主题色
- 如果有文字,建议使用大号字体

### 步骤 2: 上传图标并获取 URL

**方式 A: 使用 GitHub 仓库 (推荐,免费且稳定)**

1. 在你的 GitHub 仓库中创建图标文件夹:
   ```bash
   mkdir -p assets/icons
   ```

2. 上传图标文件:
   ```bash
   cp /path/to/your-icon.png assets/icons/app-icon.png
   git add assets/icons/app-icon.png
   git commit -m "添加应用图标"
   git push
   ```

3. 获取 Raw 链接:
   ```
   格式: https://raw.githubusercontent.com/用户名/仓库名/分支名/路径

   示例: https://raw.githubusercontent.com/dick86114/dongguaTV/main/assets/icons/app-icon.png
   ```

**方式 B: 使用图床服务**

推荐的免费图床服务:
- **imgur.com** - 国外 popular,上传后获取直接链接
- **sm.ms** - 国内可访问,简单易用
- **cloudinary.com** - 专业图片 CDN

**方式 C: 使用临时文件服务 (仅用于测试)**

⚠️ **注意**: 临时链接可能会过期,不适合生产环境

- https://temp.sh/ - 提供临时文件分享
- https://file.io/ - 临时文件存储

### 步骤 3: 触发构建并使用自定义图标

1. 访问你的 GitHub 仓库
2. 点击 **Actions** 标签
3. 选择 **Android Build & Release** 工作流
4. 点击 **Run workflow** 按钮
5. 填写表单:
   - **Server URL**: `https://your-server.com`
   - **App Name**: `我的应用`
   - **Version Tag**: `v1.0.0`
   - **自定义应用图标 URL**: `https://raw.githubusercontent.com/.../app-icon.png`
6. 点击 **Run workflow** 开始构建

## 示例场景

### 场景 1: 为不同客户定制应用

假设你为多个客户构建相同功能但品牌不同的应用:

```yaml
# 客户 A
Server URL: https://client-a.example.com
App Name: 客户A影院
Version Tag: v1.0.0
自定义应用图标 URL: https://raw.githubusercontent.com/yourrepo/assets/main/client-a-icon.png

# 客户 B
Server URL: https://client-b.example.com
App Name: 客户B影院
Version Tag: v1.0.0
自定义应用图标 URL: https://raw.githubusercontent.com/yourrepo/assets/main/client-b-icon.png
```

每次只需要修改图标 URL 和应用名称即可,无需修改代码。

### 场景 2: 测试不同图标效果

你想测试多个图标方案,看哪个效果最好:

1. 准备 3 个图标方案:
   - icon-v1.png
   - icon-v2.png
   - icon-v3.png

2. 上传到 GitHub 仓库的 assets/icons/ 目录

3. 分别构建 3 个版本进行测试:
   - v1.0.0-v1: 使用 icon-v1.png
   - v1.0.0-v2: 使用 icon-v2.png
   - v1.0.0-v3: 使用 icon-v3.png

4. 安装测试,选择最满意的版本

## 故障排查

### 问题 1: 图标下载失败

**错误信息**: `Failed to download custom icon`

**可能原因**:
- URL 不正确或无法访问
- 图片文件不存在
- 网络问题

**解决方案**:
1. 在浏览器中打开 URL,确认可以访问
2. 检查 URL 是否完整 (包括 https://)
3. 确认文件存在且可公开访问

### 问题 2: 图标显示异常

**可能原因**:
- 图片尺寸太小
- 图片格式不支持
- 图片文件损坏

**解决方案**:
1. 使用 512x512 或更大的正方形图片
2. 使用 PNG 或 JPG 格式
3. 确认图片可以正常打开

### 问题 3: 构建使用默认图标而非自定义图标

**可能原因**:
- 自定义图标 URL 字段为空
- Tag 触发的构建不支持自定义图标

**解决方案**:
1. 使用手动触发 (workflow_dispatch) 而非 Tag 触发
2. 确认填写了自定义图标 URL 字段

## 高级技巧

### 技巧 1: 使用环境变量管理图标 URL

如果你频繁使用相同的图标,可以在工作流中预设常用选项:

编辑 [.github/workflows/android-build.yml](.github/workflows/android-build.yml):

```yaml
custom_icon_url:
  description: '自定义应用图标 URL'
  required: false
  default: 'https://raw.githubusercontent.com/yourname/repo/main/default-icon.png'
```

### 技巧 2: 批量生成多个版本

结合脚本快速构建多个版本:

```bash
#!/bin/bash
# build-all-variants.sh

APP_NAMES=("客户A" "客户B" "客户C")
ICON_URLS=(
  "https://raw.githubusercontent.com/.../icon-a.png"
  "https://raw.githubusercontent.com/.../icon-b.png"
  "https://raw.githubusercontent.com/.../icon-c.png"
)

for i in "${!APP_NAMES[@]}"; do
  echo "构建 ${APP_NAMES[$i]}..."
  # 使用 GitHub CLI 触发构建
  gh workflow run android-build.yml \
    -f app_name="${APP_NAMES[$i]}" \
    -f custom_icon_url="${ICON_URLS[$i]}" \
    -f version_tag="v1.0.0-$i"
done
```

### 技巧 3: 使用 SVG 图标

虽然工作流支持 PNG/JPG,但你可以使用在线工具将 SVG 转换为 PNG:

推荐工具:
- https://cloudconvert.com/svg-to-png
- https://convertio.co/zh/svg-png/

转换参数:
- 尺寸: 1024x1024
- 格式: PNG
- 背景: 透明

## 最佳实践

1. **备份原始图标**
   - 保留高清源文件 (AI, SKetch, SVG)
   - 使用版本控制管理图标文件

2. **测试图标显示效果**
   - 在不同尺寸下测试图标
   - 在不同背景下测试 (白色/黑色背景)
   - 在实际设备上测试

3. **遵循 Android 设计规范**
   - 参考 [Android App Icons 设计指南](https://developer.android.com/guide/practices/ui_guidelines/icon_design_app)
   - 使用 Material Design 配色

4. **使用一致的命名**
   - 例如: app-icon-v1.png, app-icon-v2.png
   - 便于版本管理和追溯

## 相关资源

- [Android 图标设计指南](https://developer.android.com/guide/practices/ui_guidelines/icon_design_app)
- [Material Design 图标库](https://fonts.google.com/icons)
- [Figma - 免费设计工具](https://www.figma.com/)
- [Canva - 在线设计工具](https://www.canva.com/)

## 示例图标资源

免费图标资源网站:
- https://www.iconfinder.com/android
- https://icons8.com/icons set
- https://www.flaticon.com/

⚠️ **注意**: 使用图标时请遵守相关版权协议
