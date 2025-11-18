---
layout: default
title: Git 完全使用指南
---

# Git 完全使用指南

> 从零开始，彻底掌握 Git 版本控制

---

## 📚 目录

- [为什么需要 Git](#为什么需要-git)
- [Git 核心概念](#git-核心概念)
- [安装和配置](#安装和配置)
- [基础操作](#基础操作)
- [分支管理](#分支管理)
- [远程仓库](#远程仓库)
- [实用工作流](#实用工作流)
- [冲突解决](#冲突解决)
- [常见问题](#常见问题)
- [最佳实践](#最佳实践)
- [与 Claude Code 配合](#与-claude-code-配合)

---

## 🤔 为什么需要 Git

### 问题场景

**没有 Git 的混乱**：
```
项目文件夹/
├── index.html
├── index-backup.html
├── index-final.html
├── index-final-final.html
├── index-真的最终版.html
├── index-这次真的是最后一版.html
└── index-老板说再改一次.html  😱
```

**有了 Git**：
```
项目文件夹/
├── index.html                    # 只有一个文件
└── .git/                         # 完整的历史记录
    └── (所有版本都在这里)
```

### Git 能做什么

1. ✅ **版本控制**：随时回到任何历史版本
2. ✅ **协作开发**：多人同时开发不冲突
3. ✅ **分支管理**：并行开发多个功能
4. ✅ **备份安全**：代码永不丢失
5. ✅ **代码审查**：查看每次修改的细节
6. ✅ **团队协作**：清楚谁改了什么

---

## 🧠 Git 核心概念

### 三个区域

```
┌─────────────────────────────────────────────────────────┐
│  工作区 (Working Directory)                              │
│  你正在编辑的文件                                         │
│  ├── index.html                                         │
│  ├── style.css                                          │
│  └── app.js                                             │
└─────────────────┬───────────────────────────────────────┘
                  │ git add
                  ▼
┌─────────────────────────────────────────────────────────┐
│  暂存区 (Staging Area / Index)                          │
│  准备提交的文件快照                                       │
│  ├── index.html  ✓                                      │
│  ├── style.css   ✓                                      │
│  └── app.js      ✓                                      │
└─────────────────┬───────────────────────────────────────┘
                  │ git commit
                  ▼
┌─────────────────────────────────────────────────────────┐
│  本地仓库 (Local Repository)                            │
│  完整的项目历史                                          │
│  ├── Commit 1: 初始提交                                 │
│  ├── Commit 2: 添加样式                                 │
│  └── Commit 3: 修复bug                                  │
└─────────────────┬───────────────────────────────────────┘
                  │ git push
                  ▼
┌─────────────────────────────────────────────────────────┐
│  远程仓库 (Remote Repository)                           │
│  GitHub / GitLab / 码云                                 │
│  所有人共享的代码库                                       │
└─────────────────────────────────────────────────────────┘
```

### 文件状态

```
未跟踪 (Untracked)
    │
    │ git add
    ▼
已暂存 (Staged)
    │
    │ git commit
    ▼
已提交 (Committed)
    │
    │ 修改文件
    ▼
已修改 (Modified)
    │
    │ git add
    ▼
已暂存 (Staged)
    │
    │ git commit
    ▼
已提交 (Committed)
```

---

## ⚙️ 安装和配置

### 1. 安装 Git

**macOS**：
```bash
# 方式 1: Homebrew（推荐）
brew install git

# 方式 2: Xcode Command Line Tools
xcode-select --install
```

**检查安装**：
```bash
git --version
# 输出: git version 2.40.0
```

### 2. 全局配置

**配置用户信息**（必须）：
```bash
# 设置用户名
git config --global user.name "你的名字"

# 设置邮箱
git config --global user.email "your.email@example.com"
```

**推荐配置**：
```bash
# 设置默认分支名为 main（而不是 master）
git config --global init.defaultBranch main

# 启用颜色显示
git config --global color.ui auto

# 设置默认编辑器（可选）
git config --global core.editor "code --wait"  # VSCode
# 或
git config --global core.editor "vim"         # Vim

# 忽略文件权限变更（macOS 推荐）
git config --global core.fileMode false

# 设置中文文件名正确显示
git config --global core.quotepath false
```

**查看所有配置**：
```bash
git config --list
```

**配置别名（快捷命令）**：
```bash
# 常用别名
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

# 使用别名
git st              # 相当于 git status
git lg              # 漂亮的日志显示
```

### 3. 配置 SSH 密钥（GitHub/GitLab）

**生成 SSH 密钥**：
```bash
# 生成新的 SSH 密钥
ssh-keygen -t ed25519 -C "your.email@example.com"

# 按 Enter 使用默认位置
# 输入密码（可选，建议设置）

# 启动 ssh-agent
eval "$(ssh-agent -s)"

# 添加密钥到 ssh-agent
ssh-add ~/.ssh/id_ed25519
```

**添加到 GitHub**：
```bash
# 复制公钥到剪贴板（macOS）
pbcopy < ~/.ssh/id_ed25519.pub

# 然后去 GitHub:
# 1. 打开 https://github.com/settings/keys
# 2. 点击 "New SSH key"
# 3. 粘贴公钥
# 4. 点击 "Add SSH key"
```

**测试连接**：
```bash
ssh -T git@github.com
# 输出: Hi username! You've successfully authenticated...
```

---

## 🚀 基础操作

### 1. 创建仓库

**方式 1：初始化新仓库**
```bash
# 进入项目目录
cd /path/to/your/project

# 初始化 Git 仓库
git init

# 查看状态
git status
```

**方式 2：克隆已有仓库**
```bash
# 克隆 GitHub 仓库
git clone https://github.com/username/repo.git

# 或使用 SSH
git clone git@github.com:username/repo.git

# 克隆到指定目录
git clone https://github.com/username/repo.git my-project
```

### 2. 基本工作流程

**完整流程示例**：
```bash
# 1. 查看当前状态
git status

# 2. 创建/修改文件
echo "# 我的项目" > README.md

# 3. 查看状态（文件未跟踪）
git status
# 输出:
# Untracked files:
#   README.md

# 4. 添加到暂存区
git add README.md

# 5. 再次查看状态（文件已暂存）
git status
# 输出:
# Changes to be committed:
#   new file:   README.md

# 6. 提交到本地仓库
git commit -m "添加 README 文件"

# 7. 查看提交历史
git log
```

### 3. git add 详解

**添加单个文件**：
```bash
git add README.md
```

**添加多个文件**：
```bash
git add file1.txt file2.txt file3.txt
```

**添加所有修改的文件**：
```bash
git add .                 # 当前目录及子目录所有文件
git add -A                # 整个仓库所有文件
git add --all             # 同上
```

**添加特定类型文件**：
```bash
git add *.js              # 所有 JS 文件
git add src/*.css         # src 目录下所有 CSS 文件
```

**交互式添加**：
```bash
git add -p                # 逐块选择要添加的内容
```

**取消暂存**：
```bash
git reset HEAD file.txt   # 取消暂存某个文件
git reset HEAD .          # 取消所有暂存
```

### 4. git commit 详解

**基本提交**：
```bash
git commit -m "提交信息"
```

**多行提交信息**：
```bash
git commit -m "修复登录bug" -m "详细说明：修复了用户登录时的空指针异常"
```

**打开编辑器写提交信息**：
```bash
git commit
# 会打开配置的编辑器，可以写多行详细信息
```

**跳过暂存直接提交**（已跟踪的文件）：
```bash
git commit -a -m "提交信息"
# 或
git commit -am "提交信息"
```

**修改最后一次提交**：
```bash
# 修改提交信息
git commit --amend -m "新的提交信息"

# 添加遗漏的文件到上次提交
git add forgotten_file.txt
git commit --amend --no-edit
```

**好的提交信息规范**：
```
<type>: <subject>

<body>

<footer>
```

**示例**：
```
feat: 添加用户登录功能

- 实现用户名密码登录
- 添加记住我功能
- 集成 OAuth 第三方登录

Closes #123
```

**Type 类型**：
- `feat`: 新功能
- `fix`: 修复bug
- `docs`: 文档更新
- `style`: 代码格式调整
- `refactor`: 重构
- `test`: 测试相关
- `chore`: 构建/工具相关

### 5. git status 和 git log

**查看状态**：
```bash
git status                # 详细状态
git status -s             # 简短状态
git status --short        # 同上
```

**查看历史**：
```bash
git log                   # 完整日志
git log --oneline         # 每个提交一行
git log -3                # 最近 3 次提交
git log --since=2.weeks   # 最近两周
git log --author="张三"   # 特定作者
git log --grep="bug"      # 搜索提交信息

# 漂亮的图形化日志
git log --graph --oneline --all --decorate
```

**查看某个文件的历史**：
```bash
git log README.md         # 文件的提交历史
git log -p README.md      # 文件的修改详情
```

### 6. git diff 查看差异

**查看未暂存的修改**：
```bash
git diff
```

**查看已暂存的修改**：
```bash
git diff --staged
# 或
git diff --cached
```

**比较两个提交**：
```bash
git diff commit1 commit2
git diff HEAD~2 HEAD      # 当前版本和两个版本前
```

**比较分支**：
```bash
git diff main feature
```

### 7. 撤销操作

**撤销工作区修改**（未 add）：
```bash
git checkout -- file.txt  # 恢复单个文件
git checkout -- .         # 恢复所有文件

# 新版本推荐用
git restore file.txt
git restore .
```

**取消暂存**（已 add，未 commit）：
```bash
git reset HEAD file.txt
# 或
git restore --staged file.txt
```

**撤销提交**（已 commit）：
```bash
# 方式 1: 软重置（保留修改）
git reset --soft HEAD~1   # 撤销最后一次提交，保留修改在暂存区

# 方式 2: 混合重置（默认）
git reset HEAD~1          # 撤销提交，修改回到工作区

# 方式 3: 硬重置（完全删除）
git reset --hard HEAD~1   # 完全撤销，修改全部丢失 ⚠️

# 方式 4: revert（推荐，安全）
git revert HEAD           # 创建新提交来撤销上次修改
```

---

## 🌿 分支管理

### 为什么需要分支

```
主分支 (main)
    ├── Commit 1
    ├── Commit 2
    ├── Commit 3 ──────┐
    │                   │
    │                   ├── 功能分支 (feature)
    │                   │   ├── Commit 4
    │                   │   └── Commit 5
    │                   │
    ├── Commit 6 ◄──────┘ (合并)
    └── Commit 7
```

**优点**：
- ✅ 主分支保持稳定
- ✅ 新功能独立开发
- ✅ 可以随时切换
- ✅ 不影响他人工作

### 分支基本操作

**创建分支**：
```bash
git branch feature-login  # 创建分支
git branch               # 查看所有分支（* 表示当前分支）
```

**切换分支**：
```bash
git checkout feature-login
# 或（新版本推荐）
git switch feature-login
```

**创建并切换**：
```bash
git checkout -b feature-login
# 或
git switch -c feature-login
```

**删除分支**：
```bash
git branch -d feature-login    # 删除已合并分支
git branch -D feature-login    # 强制删除未合并分支
```

**重命名分支**：
```bash
git branch -m old-name new-name
```

### 分支合并

**场景**：在 feature 分支开发完成，合并回 main

```bash
# 1. 切换到目标分支（main）
git checkout main

# 2. 合并 feature 分支
git merge feature-login

# 3. 如果没有冲突，完成！
# 如果有冲突，见后面"冲突解决"部分
```

**两种合并方式**：

**Fast-forward（快进合并）**：
```bash
# 当前分支没有新提交时
main:    A -- B -- C
                    \
feature:             D -- E

# 合并后：
main:    A -- B -- C -- D -- E
```

**Three-way merge（三方合并）**：
```bash
# 当前分支有新提交时
main:    A -- B -- C -- F
                    \
feature:             D -- E

# 合并后：
main:    A -- B -- C -- F -- G (merge commit)
                    \       /
feature:             D -- E
```

**禁用 fast-forward**：
```bash
git merge --no-ff feature-login  # 总是创建合并提交
```

### 变基 (Rebase)

**作用**：让提交历史更清晰

```bash
# 场景：将 feature 分支的修改变基到 main
git checkout feature
git rebase main

# 效果：
# 变基前：
main:    A -- B -- C -- F
                    \
feature:             D -- E

# 变基后：
main:    A -- B -- C -- F
                         \
feature:                  D' -- E'
```

**注意**：
- ⚠️ **不要对已推送的提交进行 rebase**
- ✅ 只在本地分支使用
- ✅ 让历史更线性

**交互式 rebase**（整理提交）：
```bash
git rebase -i HEAD~3  # 整理最近 3 次提交

# 可以：
# - pick: 保留提交
# - reword: 修改提交信息
# - edit: 修改提交内容
# - squash: 合并到上一个提交
# - drop: 删除提交
```

---

## 🌐 远程仓库

### 查看远程仓库

```bash
git remote                # 查看远程仓库名称
git remote -v             # 查看远程仓库 URL
git remote show origin    # 查看详细信息
```

### 添加远程仓库

```bash
git remote add origin https://github.com/username/repo.git
# 或使用 SSH
git remote add origin git@github.com:username/repo.git
```

### 推送到远程

**首次推送**：
```bash
git push -u origin main
# -u 等同于 --set-upstream，设置上游分支
```

**后续推送**：
```bash
git push                  # 推送当前分支
git push origin main      # 推送指定分支
git push --all            # 推送所有分支
git push --tags           # 推送所有标签
```

**强制推送**（危险⚠️）：
```bash
git push --force          # 强制推送，覆盖远程
git push --force-with-lease  # 更安全的强制推送
```

### 拉取远程更新

**fetch（获取但不合并）**：
```bash
git fetch origin          # 获取远程更新
git fetch --all           # 获取所有远程仓库更新
```

**pull（获取并合并）**：
```bash
git pull                  # 相当于 fetch + merge
git pull origin main      # 拉取指定分支

# pull = fetch + merge
git pull
# 等同于
git fetch origin
git merge origin/main
```

**pull --rebase**：
```bash
git pull --rebase         # 使用 rebase 而非 merge
```

### 克隆仓库

```bash
# HTTPS
git clone https://github.com/username/repo.git

# SSH（推荐）
git clone git@github.com:username/repo.git

# 克隆指定分支
git clone -b develop git@github.com:username/repo.git

# 浅克隆（只克隆最近的提交）
git clone --depth 1 git@github.com:username/repo.git
```

---

## 💼 实用工作流

### 工作流 1：功能开发流程

```bash
# 1. 创建功能分支
git checkout -b feature/user-login

# 2. 开发功能，多次提交
git add .
git commit -m "feat: 添加登录表单"

git add .
git commit -m "feat: 实现登录验证"

git add .
git commit -m "feat: 添加记住我功能"

# 3. 推送到远程
git push -u origin feature/user-login

# 4. 创建 Pull Request（在 GitHub 上）

# 5. 代码审查通过后，合并到 main
git checkout main
git pull origin main
git merge feature/user-login

# 6. 推送合并后的 main
git push origin main

# 7. 删除功能分支
git branch -d feature/user-login
git push origin --delete feature/user-login
```

### 工作流 2：修复紧急 Bug

```bash
# 1. 从 main 创建 hotfix 分支
git checkout main
git checkout -b hotfix/critical-bug

# 2. 修复 bug
git add .
git commit -m "fix: 修复登录崩溃问题"

# 3. 合并回 main
git checkout main
git merge hotfix/critical-bug
git push origin main

# 4. 同时合并到开发分支（如果有）
git checkout develop
git merge hotfix/critical-bug
git push origin develop

# 5. 删除 hotfix 分支
git branch -d hotfix/critical-bug
```

### 工作流 3：同步远程更新

```bash
# 方式 1: pull
git checkout main
git pull origin main

# 方式 2: fetch + merge（推荐）
git fetch origin
git status                # 查看本地和远程的差异
git merge origin/main

# 方式 3: fetch + rebase（保持历史线性）
git fetch origin
git rebase origin/main
```

### 工作流 4：多人协作

**协作者 A**：
```bash
git checkout -b feature/api
# 开发...
git commit -m "feat: 添加 API 接口"
git push -u origin feature/api
```

**协作者 B**：
```bash
# 获取最新代码
git fetch origin
git checkout -b feature/api origin/feature/api

# 继续开发
git commit -m "feat: 完善 API 文档"
git push origin feature/api
```

**协作者 A**：
```bash
# 拉取 B 的更新
git pull origin feature/api
# 继续工作...
```

---

## ⚔️ 冲突解决

### 什么时候会冲突

**场景**：两个人修改了同一文件的同一部分

```bash
# 你的分支：
function login() {
    return "用户名密码登录";
}

# 同事的分支：
function login() {
    return "OAuth 登录";
}

# 合并时 Git 不知道保留哪个 → 冲突！
```

### 冲突的表现

```bash
git merge feature-branch
# 输出:
# Auto-merging index.html
# CONFLICT (content): Merge conflict in index.html
# Automatic merge failed; fix conflicts and then commit the result.
```

**查看冲突文件**：
```bash
git status
# Unmerged paths:
#   both modified:   index.html
```

### 解决冲突

**1. 打开冲突文件**，会看到：
```html
<<<<<<< HEAD (当前分支)
<h1>欢迎来到我的网站</h1>
=======
<h1>Welcome to my website</h1>
>>>>>>> feature-translation (要合并的分支)
```

**2. 手动解决**：
```html
<!-- 选项 1: 保留当前分支 -->
<h1>欢迎来到我的网站</h1>

<!-- 选项 2: 保留合并分支 -->
<h1>Welcome to my website</h1>

<!-- 选项 3: 都保留 -->
<h1>欢迎来到我的网站</h1>
<h1>Welcome to my website</h1>

<!-- 选项 4: 修改为新内容 -->
<h1>欢迎 / Welcome</h1>
```

**3. 标记为已解决**：
```bash
git add index.html
```

**4. 完成合并**：
```bash
git commit -m "解决合并冲突：统一标题文案"
```

### 使用工具解决冲突

**VSCode 内置冲突解决器**：
- 点击 "Accept Current Change" - 保留当前分支
- 点击 "Accept Incoming Change" - 保留合并分支
- 点击 "Accept Both Changes" - 都保留
- 手动编辑

**Git 命令**：
```bash
# 使用可视化工具
git mergetool

# 中止合并
git merge --abort

# 使用他们的版本
git checkout --theirs file.txt

# 使用我们的版本
git checkout --ours file.txt
```

---

## ❓ 常见问题

### 1. 如何撤销推送的提交？

```bash
# 方式 1: revert（推荐，安全）
git revert HEAD
git push origin main

# 方式 2: reset（危险，会改变历史）
git reset --hard HEAD~1
git push --force origin main  # ⚠️ 团队协作慎用
```

### 2. 如何暂存当前工作？

```bash
# 暂存工作区
git stash
git stash save "暂存描述"

# 查看暂存列表
git stash list

# 恢复暂存
git stash pop           # 恢复并删除
git stash apply         # 恢复但保留

# 删除暂存
git stash drop stash@{0}
git stash clear         # 清空所有
```

### 3. 如何查看谁改了代码？

```bash
# 查看文件每一行的修改者
git blame file.txt

# 查看某几行
git blame -L 10,20 file.txt

# 更详细的信息
git log -p file.txt
```

### 4. 如何回到某个历史版本？

```bash
# 查看提交历史
git log --oneline

# 方式 1: 临时查看
git checkout <commit-hash>

# 回到当前
git checkout main

# 方式 2: 永久回退
git reset --hard <commit-hash>
```

### 5. 忘记切换分支就开发了怎么办？

```bash
# 如果还没 commit
git stash                    # 暂存修改
git checkout feature-branch  # 切换到正确分支
git stash pop               # 恢复修改

# 如果已经 commit 了
git log -1                  # 记住 commit hash
git reset --hard HEAD~1     # 撤销提交
git checkout feature-branch # 切换分支
git cherry-pick <commit-hash>  # 拿回提交
```

### 6. 如何修改提交历史？

```bash
# 修改最后一次提交
git commit --amend

# 修改多次提交（交互式 rebase）
git rebase -i HEAD~3

# 修改作者信息
git commit --amend --author="新名字 <new@email.com>"
```

### 7. 如何删除远程分支？

```bash
git push origin --delete feature-branch
```

### 8. 大文件提交失败？

```bash
# 安装 Git LFS
brew install git-lfs
git lfs install

# 跟踪大文件
git lfs track "*.psd"
git lfs track "*.mp4"

# 正常提交
git add .gitattributes
git add large-file.psd
git commit -m "添加大文件"
git push
```

### 9. 敏感信息已提交怎么办？

```bash
# ⚠️ 危险操作，改变历史

# 从所有历史中删除文件
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch config/secrets.yml' \
  --prune-empty --tag-name-filter cat -- --all

# 强制推送
git push --force --all
```

---

## 🌟 最佳实践

### 1. Commit 原则

**频繁提交**：
- ✅ 完成一个小功能就提交
- ✅ 修复一个 bug 就提交
- ❌ 不要积累大量修改才提交

**原子性提交**：
- ✅ 一个提交只做一件事
- ✅ 方便回滚和追踪
- ❌ 不要把多个不相关的改动放在一起

**清晰的提交信息**：
```bash
# ✅ 好的提交信息
git commit -m "fix: 修复用户登录时的空指针异常"
git commit -m "feat: 添加用户头像上传功能"
git commit -m "docs: 更新 API 文档"

# ❌ 不好的提交信息
git commit -m "fix bug"
git commit -m "update"
git commit -m "wip"
```

### 2. 分支策略

**Git Flow**（适合大型项目）：
```
main (生产)
  ├── develop (开发)
  │     ├── feature/login
  │     ├── feature/payment
  │     └── feature/profile
  ├── release/v1.0
  └── hotfix/critical-bug
```

**GitHub Flow**（适合持续部署）：
```
main
  ├── feature/login
  ├── feature/payment
  └── bugfix/login-error
```

**推荐**：
- ✅ main 分支永远可部署
- ✅ 功能分支命名规范：`feature/功能名`
- ✅ Bug 修复分支：`fix/bug描述`
- ✅ 定期清理已合并分支

### 3. .gitignore 配置

**创建 .gitignore**：
```bash
# 编辑 .gitignore
cat > .gitignore << 'EOF'
# 依赖
node_modules/
vendor/

# 构建产物
dist/
build/
*.min.js
*.min.css

# 环境配置
.env
.env.local
*.secret

# 编辑器
.vscode/
.idea/
*.swp
*.swo

# 系统文件
.DS_Store
Thumbs.db

# 日志
*.log
logs/

# 数据库
*.db
*.sqlite

# Claude Code
.claude/history/
.claude/*.db
.claude/*.db-*
EOF

git add .gitignore
git commit -m "chore: 添加 .gitignore"
```

### 4. 保护主分支

**GitHub 分支保护规则**：
1. Settings → Branches → Add rule
2. 设置：
   - ✅ Require pull request reviews
   - ✅ Require status checks to pass
   - ✅ Require signed commits
   - ✅ Prevent force pushes

### 5. 标签管理

```bash
# 创建标签
git tag v1.0.0
git tag -a v1.0.0 -m "版本 1.0.0"

# 推送标签
git push origin v1.0.0
git push origin --tags

# 列出标签
git tag
git tag -l "v1.*"

# 删除标签
git tag -d v1.0.0
git push origin --delete v1.0.0
```

### 6. 团队协作规范

**Pull Request 流程**：
1. Fork 或创建分支
2. 开发功能
3. 提交 PR
4. 代码审查
5. 修改反馈
6. 合并到主分支

**代码审查清单**：
- ✅ 代码风格统一
- ✅ 没有明显 bug
- ✅ 测试覆盖
- ✅ 文档完善
- ✅ 提交信息清晰

---

## 🤖 与 Claude Code 配合

### Claude Code 的 Git 能力

Claude Code 可以帮你：
- ✅ 生成提交信息
- ✅ 创建 PR
- ✅ 审查代码
- ✅ 解决冲突
- ✅ 管理分支

### 常用工作流

**1. 让 Claude Code 提交代码**：
```
你: "把我的修改提交到 Git"

Claude:
# 查看修改
git status
git diff

# 创建提交
git add .
git commit -m "feat: 添加用户设置页面

- 实现个人信息编辑
- 添加密码修改功能
- 优化页面布局

🤖 Generated with Claude Code
Co-Authored-By: Claude <noreply@anthropic.com>"

# 推送
git push origin main
```

**2. 创建 Pull Request**：
```
你: "创建一个 PR"

Claude:
# 推送分支
git push -u origin feature/user-settings

# 使用 gh CLI 创建 PR
gh pr create --title "feat: 用户设置页面" --body "..."
```

**3. 查看 Git 历史**：
```
你: "看一下最近的提交"

Claude:
git log --oneline -10
git log --graph --oneline --all
```

### 配置 Git 权限

在 `.claude/settings.json` 中：
```json
{
  "permissions": {
    "allow": [
      "Bash(git log:*)",
      "Bash(git status:*)",
      "Bash(git diff:*)",
      "Bash(git add:*)",
      "Bash(git commit:*)",
      "Bash(git push:*)",
      "Bash(git pull:*)",
      "Bash(git branch:*)"
    ]
  }
}
```

---

## 📖 快速参考

### 常用命令速查

```bash
# 初始化和克隆
git init
git clone <url>

# 状态和差异
git status
git diff
git log

# 添加和提交
git add <file>
git add .
git commit -m "message"
git commit -am "message"

# 分支
git branch
git branch <name>
git checkout <name>
git checkout -b <name>
git merge <branch>

# 远程
git remote -v
git fetch
git pull
git push
git push -u origin <branch>

# 撤销
git checkout -- <file>
git reset HEAD <file>
git reset --hard HEAD~1
git revert HEAD

# 暂存
git stash
git stash pop
git stash list

# 标签
git tag <name>
git push origin <tag>
```

### 紧急情况救命卡

**我提交错了！**
```bash
git reset --soft HEAD~1  # 撤销提交，保留修改
```

**我改错分支了！**
```bash
git stash
git checkout correct-branch
git stash pop
```

**我想撤销所有修改！**
```bash
git reset --hard HEAD
git clean -fd
```

**我提交了敏感信息！**
```bash
git reset --hard HEAD~1  # 如果还没推送
# 如果已推送，联系管理员，重置仓库
```

**合并冲突太复杂！**
```bash
git merge --abort  # 取消合并
```

---

## 🎓 进阶学习

### 推荐资源

**书籍**：
- 《Pro Git》（中文版免费）：https://git-scm.com/book/zh/v2
- 《Git 权威指南》

**在线教程**：
- GitHub Learning Lab：https://lab.github.com/
- Learn Git Branching：https://learngitbranching.js.org/?locale=zh_CN
- Git 官方文档：https://git-scm.com/docs

**视频教程**：
- B站搜索"Git 教程"
- YouTube: Git & GitHub Tutorial for Beginners

### 进阶主题

- **Git Hooks**：自动化工作流
- **Git Submodules**：管理子项目
- **Git Worktree**：同时工作多个分支
- **Git Bisect**：二分查找 bug
- **Git Reflog**：找回丢失的提交

---

## 📝 总结

### Git 核心理念

1. **分布式**：每个人都有完整的仓库副本
2. **快照**：保存项目快照，而非差异
3. **分支**：轻量级，鼓励频繁使用
4. **完整性**：SHA-1 保证数据完整

### 日常工作流程

```bash
# 1. 同步最新代码
git pull origin main

# 2. 创建功能分支
git checkout -b feature/new-feature

# 3. 开发并提交
git add .
git commit -m "feat: 添加新功能"

# 4. 推送分支
git push -u origin feature/new-feature

# 5. 创建 PR，代码审查

# 6. 合并到主分支
git checkout main
git pull origin main
git merge feature/new-feature
git push origin main

# 7. 删除功能分支
git branch -d feature/new-feature
git push origin --delete feature/new-feature
```

### 记住这些原则

- ✅ 频繁提交，清晰描述
- ✅ 主分支保持稳定
- ✅ 使用分支开发新功能
- ✅ 提交前先 pull
- ✅ 不要提交敏感信息
- ✅ 善用 .gitignore
- ✅ 代码审查很重要

---

**开始你的 Git 之旅！**

有问题随时问 Claude Code，我会帮你解决 Git 相关的任何问题！ 🚀

---

*最后更新: 2025-11-18*
*作者: Claude Code*
