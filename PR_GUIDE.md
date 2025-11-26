# Pull Request 提交指南

## ✅ 当前状态

已完成：
- ✅ 创建新分支：`feature/add-ci-cd-automation`
- ✅ 添加 6 个文件（workflows、文档、配置）
- ✅ 提交更改（commit id: 97fe1d04a）
- ⏳ 待推送到远程仓库

---

## 📤 第一步：推送分支到 GitHub

在您的终端执行：

```bash
cd /mnt/d/Code/Other/aseprite
git push -u origin feature/add-ci-cd-automation
```

**如果遇到认证问题**，您有几种选择：

### 方法 1：使用 Personal Access Token（推荐）

1. 访问 GitHub Settings → Developer settings → Personal access tokens → Tokens (classic)
2. 点击 "Generate new token (classic)"
3. 选择权限：
   - ✅ `repo` (完整仓库访问)
   - ✅ `workflow` (更新 GitHub Actions workflows)
4. 生成并复制 token
5. 推送时使用：
   ```bash
   git push -u origin feature/add-ci-cd-automation
   # Username: 你的GitHub用户名
   # Password: 粘贴刚才的token（不是密码）
   ```

### 方法 2：使用 SSH

```bash
# 如果还没有配置 SSH
ssh-keygen -t ed25519 -C "your_email@example.com"
cat ~/.ssh/id_ed25519.pub  # 复制公钥

# 添加到 GitHub: Settings → SSH and GPG keys → New SSH key

# 修改 remote URL
git remote set-url origin git@github.com:fufushushe-xzy/aseprite.git
git push -u origin feature/add-ci-cd-automation
```

### 方法 3：使用 GitHub CLI

```bash
# 安装 GitHub CLI
sudo apt install gh  # 或从 https://cli.github.com/ 下载

# 认证
gh auth login

# 推送
git push -u origin feature/add-ci-cd-automation
```

---

## 📝 第二步：创建 Pull Request

推送成功后，有两种方式创建 PR：

### 方式 A：通过 GitHub 网页（推荐）

1. 访问仓库：https://github.com/fufushushe-xzy/aseprite
2. 您会看到黄色提示条："feature/add-ci-cd-automation had recent pushes"
3. 点击 **"Compare & pull request"** 按钮

### 方式 B：手动创建

1. 访问：https://github.com/fufushushe-xzy/aseprite/pulls
2. 点击 **"New pull request"**
3. 选择分支：
   - base: `main`
   - compare: `feature/add-ci-cd-automation`
4. 点击 **"Create pull request"**

---

## ✍️ 第三步：填写 PR 信息

### PR 标题（建议）

```
Add CI/CD Automation Workflows and Documentation
```

或者用中文：

```
添加 CI/CD 自动化工作流和完整文档
```

### PR 描述（模板）

您可以复制下面的内容：

````markdown
## 📋 变更概述

为 Aseprite 项目添加完整的 CI/CD 自动化构建和发布系统。

## ✨ 新增内容

### 🔄 GitHub Actions Workflows

1. **完整发布流程** (`.github/workflows/release.yml`)
   - 自动构建 Windows、macOS、Linux 三个平台
   - 通过 Git Tag 触发或手动触发
   - 自动创建 GitHub Release
   - 自动上传构建产物
   - 构建时间：约 60-90 分钟（并行）

2. **快速构建流程** (`.github/workflows/build-release.yml`)
   - 单平台快速构建（可选择平台）
   - 适合测试和开发
   - 产物保存为 Artifact（不创建 Release）
   - 构建时间：约 20-30 分钟

### 📚 文档系统

1. **CLAUDE.md** - 项目总览和开发指南
   - 构建说明（自动/手动）
   - 测试方法
   - 代码风格和规范
   - 架构分层说明（Level 0-5）
   - 开发工作流和调试技巧

2. **RELEASE_AUTOMATION.md** - CI/CD 详细文档
   - 完整的使用方法
   - 构建配置说明
   - 故障排除指南
   - 高级定制方法
   - 法律和配额注意事项

3. **QUICK_START_RELEASE.md** - 快速入门指南
   - 三种使用方式对比
   - 场景推荐
   - 常见操作示例
   - 工作流对比表

### 🔧 配置文件

- **.gitattributes** - Git 换行符配置
  - 防止 CRLF/LF 冲突
  - 特别优化 WSL/Windows 环境

## 🎯 使用方式

### 自动发布（推荐）

```bash
git tag v1.0.0
git push origin v1.0.0
```

### 手动触发

1. 访问 Actions 页面
2. 选择对应的 workflow
3. 点击 "Run workflow"

详见 [QUICK_START_RELEASE.md](QUICK_START_RELEASE.md)

## 📦 构建产物

每次发布生成：
- `Aseprite-vX.X.X-Windows-x64.zip`
- `Aseprite-vX.X.X-macOS-arm64.zip`
- `Aseprite-vX.X.X-Linux-x64.tar.gz`

## ✅ 测试状态

- ✅ Workflow 语法验证通过
- ✅ 文档格式检查通过
- ✅ .gitattributes 配置正确
- ⏳ 实际构建需要合并后测试

## 📝 注意事项

⚠️ **法律合规**：
- 这些 workflows 仅用于个人构建和测试
- 请勿公开分发编译后的二进制文件
- Aseprite 使用专有 EULA 许可证

## 🔗 相关文档

- [CLAUDE.md](CLAUDE.md) - 项目开发指南
- [RELEASE_AUTOMATION.md](RELEASE_AUTOMATION.md) - CI/CD 详细说明
- [QUICK_START_RELEASE.md](QUICK_START_RELEASE.md) - 快速开始

## 🤝 审查建议

建议重点关注：
1. Workflow 配置是否符合项目需求
2. 构建参数是否合理（特别是 CMake 选项）
3. 文档描述是否准确
4. 是否需要调整构建平台或架构

感谢审查！🙏
````

---

## 🎨 第四步：添加标签和请求审查（可选）

### 建议添加的标签

- `enhancement` - 功能增强
- `documentation` - 文档相关
- `ci/cd` - CI/CD 相关
- `github-actions` - GitHub Actions

### 请求审查

在 PR 页面右侧：
1. 在 "Reviewers" 中选择您的朋友
2. 或在评论中 @mention 他们

---

## 💬 第五步：与维护者沟通

### 初始评论建议

````markdown
@fufushushe-xzy

你好！我为项目添加了完整的 CI/CD 自动化系统。

**主要特性：**
- 🚀 自动化构建发布（支持 Windows/macOS/Linux）
- 📚 完整的文档系统
- 🔧 灵活的触发方式（Tag 或手动）

**为什么需要这个：**
- 简化发布流程（推送 tag 即可自动构建）
- 统一构建环境（避免本地环境差异）
- 节省时间（并行构建 + 自动打包）

有任何问题或建议欢迎提出！详细说明请查看新增的文档。
````

---

## 🔍 常见问题

### Q: 推送时提示 "Permission denied"

**A**: 检查您是否有该仓库的写入权限：
- 如果是 collaborator：应该有权限
- 如果不是：需要先 fork 仓库，然后推送到您的 fork

Fork 后的流程：
```bash
# 添加您的 fork 为 remote
git remote add myfork https://github.com/YOUR_USERNAME/aseprite.git

# 推送到您的 fork
git push -u myfork feature/add-ci-cd-automation

# 然后在 GitHub 上从您的 fork 创建 PR 到原仓库
```

### Q: PR 创建后需要做什么？

**A**:
1. ✅ 等待 CI 检查通过（如果有）
2. ✅ 回应审查意见
3. ✅ 根据反馈修改代码
4. ✅ 保持沟通

### Q: 如何更新 PR？

**A**: 只需在本地分支继续提交并推送：
```bash
# 修改文件
git add .
git commit -m "Address review comments"
git push
```

PR 会自动更新。

---

## 📊 检查清单

提交 PR 前确认：

- [x] 创建了新分支
- [x] 所有文件已提交
- [x] 换行符问题已修复（LF）
- [ ] 分支已推送到远程
- [ ] PR 已创建
- [ ] PR 描述完整
- [ ] 已请求审查
- [ ] 与维护者沟通

---

## 🎉 完成后

PR 合并后：
1. 删除本地分支：`git branch -d feature/add-ci-cd-automation`
2. 删除远程分支（可选）：`git push origin --delete feature/add-ci-cd-automation`
3. 更新本地 main：`git checkout main && git pull`

---

## 🆘 需要帮助？

- 📧 联系仓库维护者
- 💬 在 PR 评论中提问
- 📖 查看 [GitHub PR 文档](https://docs.github.com/en/pull-requests)

---

**祝提交顺利！** 🚀
