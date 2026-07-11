# Git — 版本控制工具

## 什么是 Git？

**Git** 是一个**分布式版本控制系统**，由 Linus Torvalds 于 2005 年创建，用于高效地管理项目的代码变更历史。

### 核心作用

| 作用 | 说明 |
|------|------|
| **版本追踪** | 记录文件的每一次修改，可以随时回退到任意历史版本 |
| **协作开发** | 多人同时开发同一项目，互不干扰，最后合并代码 |
| **分支管理** | 创建独立分支开发新功能，完成后合并回主分支 |
| **备份与同步** | 配合远程仓库（如 GitHub、GitLab）备份代码并跨设备同步 |
| **代码审查** | 通过 Pull Request / Merge Request 进行代码评审 |

---

## 基本工作流程

```
工作区 (Working Directory)  →  暂存区 (Staging Area)  →  本地仓库 (Local Repo)  →  远程仓库 (Remote Repo)
     │                               │                        │                          │
  修改文件                      git add                   git commit                  git push
```

---

## 常见命令

### 🔧 配置

```bash
git config --global user.name "Your Name"       # 设置用户名
git config --global user.email "your@email.com"  # 设置邮箱
git config --global core.editor "code --wait"    # 设置默认编辑器为 VS Code
git config --list                                # 查看所有配置
```

### 🚀 仓库操作

```bash
git init                    # 初始化本地仓库
git clone <url>             # 克隆远程仓库到本地
git clone <url> <文件夹名>   # 克隆到指定目录
```

### 📝 日常操作

```bash
git status                  # 查看工作区状态（哪些文件被修改/暂存）
git add <file>              # 将文件添加到暂存区
git add .                   # 添加所有变更到暂存区
git commit -m "信息"        # 将暂存区内容提交到本地仓库
git commit -am "信息"       # 跳过 git add，直接提交已跟踪文件的修改
```

### 🔄 查看历史

```bash
git log                     # 查看提交历史
git log --oneline           # 简洁模式查看历史（一行一个 commit）
git log --graph --oneline   # 图形化查看分支历史
git diff                    # 查看工作区与暂存区的差异
git diff --staged           # 查看暂存区与上次提交的差异
```

### 🌿 分支管理

```bash
git branch                  # 查看本地分支列表（* 表示当前分支）
git branch <name>           # 创建新分支
git switch <name>           # 切换到指定分支（新版 Git 推荐）
git checkout <name>         # 切换到指定分支（传统写法）
git switch -c <name>        # 创建并切换到新分支
git merge <name>            # 将指定分支合并到当前分支
git branch -d <name>        # 删除分支
```

### ⏪ 撤销与回退

```bash
git restore <file>          # 撤销工作区的修改（新版）
git checkout -- <file>      # 撤销工作区的修改（传统版）
git restore --staged <file> # 将文件从暂存区移出
git reset --soft HEAD~1     # 撤销最近一次 commit，保留更改在暂存区
git reset --mixed HEAD~1    # 撤销最近一次 commit，保留更改在工作区（默认）
git reset --hard HEAD~1     # 彻底撤销最近一次 commit，丢弃更改（⚠️ 谨慎使用）
```

### ☁️ 远程仓库
git init时只会初始化创建一个本地仓库，若要链接远程仓库，比如链接GitHub商店仓库，用到如下的一些指令

```bash
git remote -v                            # 查看远程仓库地址
git remote add origin <url>              # 添加远程仓库
git push origin <分支名>                  # 推送代码到远程
git push -u origin <分支名>               # 推送并设置上游关联（后续只需 git push）
git pull                                  # 拉取远程最新代码并合并
git fetch                                 # 拉取远程最新代码但不合并
git push origin --delete <分支名>          # 删除远程分支
```

### 📦 暂存（Stash）

```bash
git stash                 # 临时保存工作区的修改（回到干净状态）
git stash list            # 查看暂存列表
git stash pop             # 恢复最近一次暂存并删除它
git stash apply           # 恢复最近一次暂存但不删除
git stash drop            # 删除最近一次暂存
```

---

## 实用工作流示例

### 场景：开发新功能

```bash
git checkout main                  # 回到主分支
git pull                           # 拉取最新代码
git switch -c feature/login        # 创建并切换到新分支
# ... 修改代码 ...
git add .
git commit -m "feat: 添加登录功能"
git push -u origin feature/login   # 推送到远程
# → 然后在 GitHub 上创建 Pull Request
```

### 场景：修复紧急 Bug

```bash
git stash                          # 保存当前工作区
git switch -c hotfix/crash-fix     # 从 main 创建修复分支
# ... 修复代码 ...
git commit -am "fix: 修复崩溃问题"
git switch main
git merge hotfix/crash-fix         # 合并回主分支
git push
git branch -d hotfix/crash-fix     # 删除本地分支
git stash pop                      # 恢复之前的工作
```

---

## 提交信息规范（Conventional Commits）

推荐使用规范的提交信息格式：

```
<type>: <简短描述>

<可选详细说明>
```

| Type | 说明 |
|------|------|
| `feat` | 新功能 |
| `fix` | 修复 Bug |
| `docs` | 文档变更 |
| `refactor` | 代码重构 |
| `style` | 格式调整（不影响功能） |
| `test` | 测试相关 |
| `chore` | 杂项（构建、CI 等） |

**示例：**
```
feat: 添加用户登录功能
fix: 修复首页白屏问题
docs: 更新 API 使用说明
```

---

## 🍴 Fork — 复刻与协作

### 什么是 Fork？

**Fork（复刻）** 是 GitHub/GitLab 等代码托管平台提供的一项功能，指的是**将别人的仓库复制一份到自己的账户下**，形成一个与原仓库独立的副本。

Fork 与 Clone 的区别：

| 操作 | 仓库归属 | 目的 |
|------|---------|------|
| **Fork** | 复制到**自己的远程账户** | 在别人的项目上独立开发，然后通过 PR 贡献代码 |
| **Clone** | 下载到**本地电脑** | 在本地开发已有的远程仓库 |

Fork 的核心工作流：

```
原仓库 (upstream)
    ↓ Fork
自己的远程仓库 (origin)
    ↓ Clone
本地仓库
    ↓ 开发 → Commit → Push
自己的远程仓库 (origin)
    ↓ Pull Request
原仓库 (upstream) 审核合并
```

### 何时使用 Fork？

| 场景 | 说明 |
|------|------|
| **参与开源项目** | 给别人的开源项目贡献代码 |
| **基于他人项目做二次开发** | 把别人的项目 Fork 后，改成自己的版本 |
| **团队协作** | 团队成员各自 Fork 项目，通过 PR 合并代码（比直接推送更安全） |

### Fork 的完整操作流程

#### 场景：参与一个开源项目

假设你想给 `vuejs/core` 贡献代码：

##### 第 1 步：Fork 到自己的账户

1. 打开 https://github.com/vuejs/core
2. 点击右上角的 **Fork** 按钮
3. 选择 Fork 到你的个人账户（如 `你的用户名/core`）

##### 第 2 步：Clone 到本地

```bash
# 克隆你自己账户下的 Fork 仓库
git clone https://github.com/你的用户名/core.git
cd core

# 查看当前远程仓库
git remote -v
# origin → https://github.com/你的用户名/core.git
```

##### 第 3 步：添加原仓库为上游（upstream）

```bash
# 添加原仓库为 upstream（上游）
git remote add upstream https://github.com/vuejs/core.git

# 确认远程仓库配置
git remote -v
# origin    → https://github.com/你的用户名/core.git (push/fetch)
# upstream → https://github.com/vuejs/core.git   (push/fetch)
```

##### 第 4 步：基于最新代码创建分支

```bash
# 拉取原仓库的最新代码
git fetch upstream

# 基于原仓库 main 分支创建新分支
git switch -c fix/typo upstream/main

# ... 修改代码，修复拼写错误 ...
git add .
git commit -m "fix: 修复文档中的拼写错误"
git push -u origin fix/typo
```

##### 第 5 步：发起 Pull Request

1. 打开 GitHub，进入你的 Fork 仓库
2. 页面顶部会提示 "fix/typo 刚刚推送，比较并创建 PR"
3. 点击 **Compare & pull request**
4. 填写 PR 标题和描述
5. 点击 **Create pull request**

##### 第 6 步：同步原仓库更新

```bash
# 如果原仓库有新提交，同步到你的 Fork
git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

### 完整工作流示例

```bash
# ===================== 初始设置 =====================
# Fork 仓库后，克隆到本地
git clone https://github.com/你的用户名/awesome-project.git
cd awesome-project

# 添加原仓库为上游
git remote add upstream https://github.com/原作者/awesome-project.git

# ===================== 日常开发 =====================
# 同步最新代码
git fetch upstream
git switch main
git merge upstream/main
git push origin main

# 创建功能分支
git switch -c feat/new-feature upstream/main

# ... 开发代码 ...
git add .
git commit -m "feat: 添加新功能"
git push -u origin feat/new-feature

# → 在 GitHub 上创建 Pull Request
# → 等待原作者审核合并

# ===================== PR 合并后 =====================
# 删除远程功能分支
git push origin --delete feat/new-feature
# 删除本地功能分支
git branch -d feat/new-feature
# 同步原仓库最新更新
git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

### Fork 与 直接 Clone + Push 的对比

| 特性 | Fork 工作流 | 直接推送 |
|------|------------|---------|
| **权限要求** | 无需原仓库写权限 | 需要原仓库写权限 |
| **安全性** | 原仓库代码不会被意外破坏 | 高风险（可能误推送） |
| **贡献流程** | 通过 Pull Request 提交 | 可直接推送分支 |
| **适用场景** | 开源贡献、外部协作 | 团队内部协作 |
| **隔离性** | 完全独立，各自维护 | 共享同一个仓库 |

### 保持 Fork 同步的最佳实践

```bash
# 1. 配置两个远程仓库
git remote add upstream <原仓库URL>

# 2. 定期同步（建议每次开发前执行）
git fetch upstream
git switch main
git merge upstream/main
git push origin main

# 3. 从最新的 main 创建功能分支，避免冲突
git switch -c feat/new-feature upstream/main
```

---

## 学习资源

- [官方文档](https://git-scm.com/doc) — 最权威的 Git 文档
- [Pro Git 中文版](https://git-scm.com/book/zh/v2) — 免费电子书
- [Learn Git Branching](https://learngitbranching.js.org/) — 交互式学习 Git 分支
