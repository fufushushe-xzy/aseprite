# Aseprite 自动化发布指南

本文档说明如何使用 GitHub Actions 实现 Aseprite 的自动化编译和发布。

## 概述

已为此仓库配置了完整的自动化发布流程，包括：
- ✅ 自动构建 Windows、macOS、Linux 三个平台的发布版本
- ✅ 自动打包构建产物
- ✅ 自动创建 GitHub Release
- ✅ 自动上传构建文件到 Release

## 使用方法

### 方式一：通过 Git Tag 触发（推荐）

这是最标准的发布方式：

```bash
# 1. 确保代码已提交
git add .
git commit -m "Prepare release v1.0.0"
git push

# 2. 创建并推送 tag
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

推送 tag 后，GitHub Actions 会自动：
1. 创建名为 "Aseprite v1.0.0" 的 Release
2. 并行构建三个平台的版本
3. 将构建产物上传到 Release

### 方式二：手动触发

如果需要手动触发发布：

1. 访问仓库的 Actions 页面
2. 选择 "Release Build and Publish" workflow
3. 点击 "Run workflow"
4. 输入版本号（如 `v1.0.0`）
5. 点击 "Run workflow" 按钮

## 构建产物

每次发布会生成以下文件：

| 平台 | 文件名 | 说明 |
|------|--------|------|
| Windows | `Aseprite-vX.X.X-Windows-x64.zip` | Windows 64位版本 |
| macOS | `Aseprite-vX.X.X-macOS-arm64.zip` | macOS Apple Silicon版本 |
| Linux | `Aseprite-vX.X.X-Linux-x64.tar.gz` | Linux 64位版本 |

每个包都包含：
- 编译好的可执行文件（`build/bin/`）
- 数据文件（`data/` 目录，包括主题、扩展、字体等）

## 版本号规范

建议使用语义化版本号：`vMAJOR.MINOR.PATCH`

- **MAJOR**：重大功能变更或不兼容的 API 修改
- **MINOR**：向后兼容的功能新增
- **PATCH**：向后兼容的问题修复

示例：
- `v1.0.0` - 首个正式版本
- `v1.1.0` - 添加新功能
- `v1.1.1` - 修复 bug
- `v2.0.0` - 重大更新

## 构建配置说明

### 已启用的功能
- ✅ **Lua 脚本支持** (`ENABLE_SCRIPTING=ON`)
- ✅ **Skia 渲染后端** (`LAF_BACKEND=skia`)
- ✅ **Release 优化** (`CMAKE_BUILD_TYPE=Release`)

### 已禁用的功能
- ❌ 单元测试 (`ENABLE_TESTS=OFF`) - 减小包体积
- ❌ 自动更新检查 (`ENABLE_UPDATER=OFF`) - 避免指向官方服务器
- ❌ CCache (`ENABLE_CCACHE=OFF` on Windows) - Windows 不支持

### 平台特定配置

#### Windows
- 使用 Visual Studio 2022 工具链
- 64位构建 (x64)
- 使用 Ninja 构建系统

#### macOS
- Apple Silicon (ARM64) 架构
- 最低支持 macOS 11.0
- 启用 ARM NEON 优化

#### Linux
- 使用 Clang 编译器
- libstdc++ 标准库
- 64位构建 (x64)

## 构建时间估算

| 平台 | 估计时间 |
|------|----------|
| Windows | ~20-30 分钟 |
| macOS | ~20-30 分钟 |
| Linux | ~15-25 分钟 |
| **总计** | **~60-90 分钟** |

三个平台并行构建，总时间取决于最慢的那个。

## 故障排除

### 构建失败

1. **Skia 下载失败**
   ```
   检查 laf/misc/skia-url.sh 是否正确
   可能需要更新 Skia 版本或 URL
   ```

2. **依赖项缺失**（Linux）
   ```
   确保 .github/workflows/release.yml 中的依赖项列表完整
   ```

3. **编译错误**
   ```
   检查是否有未提交的代码变更
   确保 submodules 正确更新
   ```

### Release 创建失败

- **权限问题**：确保仓库有 Actions 的写权限
  - 进入 Settings → Actions → General
  - 确保 "Workflow permissions" 设置为 "Read and write permissions"

- **Tag 已存在**：不能重复使用相同的 tag
  ```bash
  # 删除本地 tag
  git tag -d v1.0.0
  # 删除远程 tag
  git push origin :refs/tags/v1.0.0
  ```

### 文件上传失败

检查文件路径是否正确：
```bash
# 检查构建产物
ls -la package/
```

## 高级定制

### 修改构建配置

编辑 `.github/workflows/release.yml` 中的 CMake 参数：

```yaml
cmake -S . -B build -G Ninja \
  -DCMAKE_BUILD_TYPE=Release \
  -DENABLE_XXXX=ON/OFF    # 修改功能开关
  ...
```

常用选项：
- `ENABLE_UPDATER` - 自动更新检查
- `ENABLE_STEAM` - Steam 集成
- `ENABLE_WEBSOCKET` - WebSocket 支持
- `ENABLE_DEVMODE` - 开发者模式

### 添加额外平台

复制现有的 `build-*` job 并修改：
1. 改变 `runs-on` 指定的运行环境
2. 调整依赖项安装步骤
3. 修改 CMake 配置参数
4. 更新打包命令

### 修改 Release 说明

编辑 `create-release` job 中的 `body` 字段：

```yaml
body: |
  ## 自定义发布说明

  ### 新功能
  - 功能 1
  - 功能 2

  ### 修复问题
  - 修复 1
  - 修复 2
```

## 注意事项

### 法律合规

⚠️ **重要**：Aseprite 使用专有 EULA 许可证：

- 源代码和二进制文件受 [EULA](EULA.txt) 保护
- 只能用于个人学习和研究
- **禁止分发编译后的二进制文件**
- **禁止商业使用**

如果您 fork 此仓库：
- ✅ 可以为自己构建和使用
- ❌ 不得公开分发构建产物
- ❌ 不得商业化使用

### GitHub Actions 配额

- 公开仓库：无限制的 Actions 运行时间
- 私有仓库：
  - 免费账户：2,000 分钟/月
  - Pro 账户：3,000 分钟/月

每次完整发布消耗约 60-90 分钟。

### 存储空间

每个 Release 的文件大小约：
- Windows: ~25-35 MB
- macOS: ~25-35 MB
- Linux: ~20-30 MB
- **总计**: ~70-100 MB/版本

GitHub 免费账户有 500MB Release 存储限制。

## 最佳实践

1. **版本标记**
   - 每次发布前打 tag
   - 使用语义化版本号
   - 在 tag message 中写明更新内容

2. **测试**
   - 发布前在本地测试构建
   - 使用 Draft Release 进行预发布测试
   - 让社区测试 beta 版本

3. **文档更新**
   - 更新 README.md 中的版本信息
   - 维护 CHANGELOG.md
   - 记录已知问题

4. **备份**
   - 定期备份重要的 Release 文件
   - 保留构建日志以便调试

## 参考资料

- [GitHub Actions 文档](https://docs.github.com/en/actions)
- [Aseprite 编译指南](INSTALL.md)
- [语义化版本规范](https://semver.org/)
- [Aseprite 官方网站](https://www.aseprite.org/)
