# 快速开始：自动化发布

本指南帮助您快速上手使用 GitHub Actions 自动化构建和发布 Aseprite。

## 📋 前提条件

1. **Fork 本仓库**到您的 GitHub 账户
2. **克隆仓库**到本地：
   ```bash
   git clone --recursive https://github.com/YOUR_USERNAME/aseprite.git
   cd aseprite
   ```

3. **配置 Actions 权限**：
   - 访问仓库 Settings → Actions → General
   - 在 "Workflow permissions" 中选择 "Read and write permissions"
   - 勾选 "Allow GitHub Actions to create and approve pull requests"
   - 点击 "Save"

## 🚀 三种使用方式

### 方式 1️⃣：完整发布（推荐用于正式版本）

适用场景：发布正式版本，自动创建 Release 并上传所有平台

```bash
# 1. 提交代码
git add .
git commit -m "Release v1.0.0"
git push

# 2. 创建并推送 tag
git tag v1.0.0
git push origin v1.0.0
```

✨ **自动执行**：
- 创建 GitHub Release
- 构建 Windows + macOS + Linux
- 上传所有构建文件到 Release

📦 **产物位置**：Release 页面

⏱️ **构建时间**：约 60-90 分钟（并行）

---

### 方式 2️⃣：快速构建（推荐用于测试）

适用场景：测试构建、个人使用，不创建 Release

**触发方式：**
1. 访问仓库的 Actions 页面
2. 选择 "Quick Build Release"
3. 点击 "Run workflow"
4. 选择要构建的平台（Linux/Windows/macOS）
5. 点击绿色的 "Run workflow" 按钮

✨ **自动执行**：
- 构建单个平台
- 上传构建文件为 Artifact

📦 **产物位置**：Actions 页面 → 对应的 workflow run → Artifacts

⏱️ **构建时间**：约 20-30 分钟

---

### 方式 3️⃣：手动触发完整发布

适用场景：需要手动控制发布时机

**触发方式：**
1. 访问仓库的 Actions 页面
2. 选择 "Release Build and Publish"
3. 点击 "Run workflow"
4. 输入版本号（如 `v1.0.0`）
5. 点击绿色的 "Run workflow" 按钮

✨ **自动执行**：同方式 1️⃣

📦 **产物位置**：Release 页面

⏱️ **构建时间**：约 60-90 分钟

---

## 📥 下载构建产物

### 从 Release 下载（方式 1️⃣ 和 3️⃣）

1. 访问仓库的 "Releases" 页面
2. 找到对应版本（如 v1.0.0）
3. 在 "Assets" 部分下载：
   - `Aseprite-vX.X.X-Windows-x64.zip`
   - `Aseprite-vX.X.X-macOS-arm64.zip`
   - `Aseprite-vX.X.X-Linux-x64.tar.gz`

### 从 Artifacts 下载（方式 2️⃣）

1. 访问 Actions 页面
2. 点击对应的 workflow run（绿色✓或红色✗）
3. 滚动到底部的 "Artifacts" 部分
4. 下载对应平台的文件

⚠️ **注意**：Artifacts 会在 30 天后自动删除

---

## 🎯 使用场景推荐

| 场景 | 推荐方式 | 说明 |
|------|----------|------|
| 正式发布 | 方式 1️⃣ | 创建 tag，自动发布 |
| 快速测试 | 方式 2️⃣ | 测试单个平台，不污染 Release |
| 预发布测试 | 方式 3️⃣ | 手动控制，测试完整流程 |
| 个人使用 | 方式 2️⃣ | 只构建需要的平台 |
| 修复紧急 bug | 方式 1️⃣ | 快速发布补丁版本 |

---

## 🛠️ 常见操作

### 删除错误的 Release

```bash
# 删除 tag
git tag -d v1.0.0
git push origin :refs/tags/v1.0.0

# 在 GitHub 上手动删除 Release
# 访问 Releases 页面 → 找到对应版本 → Delete
```

### 查看构建日志

1. 访问 Actions 页面
2. 点击对应的 workflow run
3. 点击具体的 job（如 "build-windows"）
4. 查看各个步骤的详细日志

### 取消正在运行的构建

1. 访问 Actions 页面
2. 点击正在运行的 workflow（黄色圆圈）
3. 右上角点击 "Cancel workflow"

---

## 📊 工作流对比

| 特性 | release.yml | build-release.yml |
|------|-------------|-------------------|
| **文件名** | Release Build and Publish | Quick Build Release |
| **触发条件** | Tag 推送 / 手动 | Push / PR / 手动 |
| **构建平台** | 全部（3个） | 单个（可选） |
| **创建 Release** | ✅ 是 | ❌ 否 |
| **产物类型** | Release 资产 | Artifact |
| **保存时间** | 永久 | 30 天 |
| **适用场景** | 正式发布 | 测试/开发 |
| **构建时间** | 60-90 分钟 | 20-30 分钟 |

---

## ⚠️ 重要提醒

### 法律合规

> **Aseprite 使用专有 EULA 许可证，源代码和二进制文件受保护。**

✅ **允许**：
- 个人学习和研究
- 为自己构建和使用
- 修改源代码（个人使用）

❌ **禁止**：
- 公开分发编译后的二进制文件
- 商业使用
- 作为其他软件的一部分分发
- 在未授权的情况下销售

如果您想合法地获得 Aseprite：
- [Steam 购买](https://store.steampowered.com/app/431730/Aseprite/)
- [itch.io 购买](https://aseprite.itch.io/aseprite)
- [官方网站购买](https://www.aseprite.org/)

### Actions 配额

| 账户类型 | 免费额度 | 说明 |
|----------|---------|------|
| 公开仓库 | ♾️ 无限制 | 免费 |
| 私有仓库（Free） | 2,000 分钟/月 | 约 20-30 次完整发布 |
| 私有仓库（Pro） | 3,000 分钟/月 | 约 30-50 次完整发布 |

💡 **节省配额建议**：
- 使用"快速构建"只构建需要的平台
- 本地测试后再触发 CI
- 合并多个小改动后一起发布

---

## 🔧 自定义配置

如需修改构建配置，编辑对应的 workflow 文件：

```bash
# 完整发布配置
.github/workflows/release.yml

# 快速构建配置
.github/workflows/build-release.yml
```

常见修改：
- CMake 编译选项（如启用/禁用特定功能）
- 构建类型（Release/Debug/RelWithDebInfo）
- 打包方式和文件结构
- Release 描述和格式

详细说明请参考：[RELEASE_AUTOMATION.md](RELEASE_AUTOMATION.md)

---

## 📚 相关文档

- 📘 [完整自动化发布文档](RELEASE_AUTOMATION.md) - 详细的配置和故障排除
- 📗 [项目总览](CLAUDE.md) - Aseprite 项目架构和开发指南
- 📕 [编译指南](INSTALL.md) - 手动编译说明
- 📙 [贡献指南](CONTRIBUTING.md) - 如何为项目做贡献

---

## 💡 提示

1. **首次使用**建议先用"快速构建"测试流程
2. **正式发布**前确保代码通过所有测试
3. **版本号**遵循语义化版本规范（v1.2.3）
4. **Release notes**可以在创建后手动编辑补充
5. **定期清理**旧的 Releases 以节省存储空间

---

## 🆘 需要帮助？

- 🐛 [提交 Issue](https://github.com/aseprite/aseprite/issues)
- 💬 [社区讨论](https://community.aseprite.org/)
- 📖 查看 [GitHub Actions 文档](https://docs.github.com/en/actions)

---

**祝您构建顺利！** 🎉
