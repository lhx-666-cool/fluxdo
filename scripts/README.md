# 发版脚本使用说明

## 功能

自动化发版流程，包括：
- ✅ 验证版本号格式
- ✅ 检查 git 状态（未提交的更改、分支等）
- ✅ 自动生成 Version Code（基于日期时间）
- ✅ 更新 `pubspec.yaml` 版本号
- ✅ 创建 git commit 和 tag
- ✅ 推送到远程仓库
- ✅ 触发 CI 自动构建和发布

## 使用方法

### Linux/macOS

```bash
# 赋予执行权限（首次使用）
chmod +x scripts/release.sh

# 发布稳定版
./scripts/release.sh 0.1.0

# 发布预发布版
./scripts/release.sh 0.1.0-beta --pre
./scripts/release.sh 0.2.0-rc1 --pre
```

### Windows

```cmd
# 发布稳定版
powershell -ExecutionPolicy Bypass -File scripts\release.ps1 0.1.0

# 发布预发布版
powershell -ExecutionPolicy Bypass -File scripts\release.ps1 0.1.0-beta --pre
powershell -ExecutionPolicy Bypass -File scripts\release.ps1 0.2.0-rc1 --pre
```

## 版本号规则

### 格式

```
主版本.次版本.修订号[-预发布标识]

示例：
0.1.0        - 稳定版
0.1.0-beta   - Beta 测试版
0.1.0-rc1    - Release Candidate 1
1.0.0-alpha  - Alpha 测试版
```

### 语义化版本

- **主版本号**：不兼容的 API 修改
- **次版本号**：向下兼容的功能性新增
- **修订号**：向下兼容的问题修正

### Version Code

自动生成，格式：`YYYYMMDDhh`

示例：
- `2026012414` = 2026年1月24日14时

## 发版流程

### 1. 准备阶段

```bash
# 确保所有更改已提交
git status

# 确保在 main 分支
git checkout main

# 拉取最新代码
git pull
```

### 2. 运行脚本

```bash
# Linux/macOS
./scripts/release.sh 0.1.0

# Windows
powershell -ExecutionPolicy Bypass -File scripts/release.ps1 0.1.0
```

### 3. 脚本执行流程

1. **验证环境**
   - 检查版本号格式
   - 检查 git 状态
   - 检查当前分支
   - 检查 tag 是否已存在

2. **显示发版信息**
   ```
   ==========================================
     发版信息
   ==========================================
   版本号: 0.1.0
   Version Name: 0.1.0
   Version Code: 2026012414
   类型: 稳定版
   分支: main
   ==========================================
   ```

3. **确认发版**
   - 输入 `y` 确认
   - 输入 `n` 取消

4. **自动执行**
   - 更新 `pubspec.yaml`
   - 创建 commit
   - 推送到远程
   - 创建并推送 tag

5. **触发 CI**
   - GitHub Actions 自动构建
   - 生成 Changelog（稳定版）
   - 创建 Release
   - 发送 Telegram 通知（如已配置）

### 4. 验证发布

访问以下链接查看构建状态：
- GitHub Actions: https://github.com/Lingyan000/fluxdo/actions
- Releases: https://github.com/Lingyan000/fluxdo/releases

## 稳定版 vs 预发布版

### 稳定版（无 `-` 后缀）

```bash
./scripts/release.sh 0.1.0
```

**特性**：
- ✅ 自动生成 Changelog
- ✅ 生成 SHA256 校验和
- ✅ Release 标记为正式版
- ✅ 发送 Telegram 通知（🚀）

### 预发布版（有 `-` 后缀）

```bash
./scripts/release.sh 0.1.0-beta --pre
```

**特性**：
- ❌ 不生成 Changelog
- ❌ 不生成 SHA256 校验和
- ✅ Release 标记为 prerelease
- ✅ 发送 Telegram 通知（🧪）

## 常见问题

### Q: 如何删除错误的 tag？

```bash
# 删除本地 tag
git tag -d v0.1.0

# 删除远程 tag
git push origin :refs/tags/v0.1.0

# 删除 GitHub Release（手动在网页上删除）
```

### Q: 如何回滚版本号？

```bash
# 1. 删除错误的 tag（见上）
# 2. 回滚 commit
git reset --hard HEAD~1
git push --force

# 3. 重新运行脚本
./scripts/release.sh 0.1.0
```

### Q: 脚本执行失败怎么办？

检查以下几点：
1. 是否有未提交的更改
2. 是否在正确的分支
3. Tag 是否已存在
4. 网络连接是否正常

### Q: 如何跳过某些检查？

不建议跳过检查，但如果必须：
- 编辑脚本，注释掉相应的检查代码
- 或者手动执行发版步骤

## 手动发版（不使用脚本）

如果脚本无法使用，可以手动执行：

```bash
# 1. 更新版本号
# 编辑 pubspec.yaml，修改 version 字段

# 2. 提交更改
git add pubspec.yaml
git commit -m "chore: bump version to 0.1.0"
git push

# 3. 创建并推送 tag
git tag -a v0.1.0 -m "Release v0.1.0"
git push origin v0.1.0
```

## 注意事项

1. **版本号必须递增**：新版本号必须大于当前版本
2. **Tag 不可重复**：相同的 tag 只能创建一次
3. **Changelog 自动生成**：稳定版会自动更新 CHANGELOG.md
4. **备份重要数据**：发版前确保代码已备份
5. **测试充分**：预发布版充分测试后再发布稳定版

## 相关文档

- [GitHub Actions 工作流](.github/workflows/release-appimage.yaml)
- [Release Template](.github/release_template.md)
- [Changelog](../CHANGELOG.md)
