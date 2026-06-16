# Netbox Submodule 工作流指南

本项目通过 **Fork + Submodule** 方式管理 Netbox 源码，允许自由修改代码的同时跟踪官方更新。

## 架构说明

```
GitHub 官方仓库 (netbox-community/netbox)  ← 只读，用于同步
        ↓ Fork
GitHub 个人仓库 (<用户名>/netbox)          ← 可读写，存放你的修改
        ↓ Submodule
本地 xuntian/netbox/                        ← 子模块，指向 Fork 的某个 commit
```

## 初始化（新成员）

```powershell
# 克隆项目（含子模块）
git clone --recurse-submodules <xuntian 仓库 URL>

# 如果已经克隆但子模块为空
git submodule update --init --recursive
```

---

## 日常操作

### 1. 修改 Netbox 代码 → 推送到你的 Fork

```powershell
# Step 1: 进入子模块
cd netbox

# Step 2: 正常修改代码...

# Step 3: 提交到你的 Fork
git add .
git commit -m "描述你的修改内容"
git push origin main

# Step 4: 回到外层仓库，锁定新版本
cd ..
git add netbox
git commit -m "更新 netbox 子模块：描述你的修改"
git push
```

> ⚠️ **重要**：Step 3 把代码推到你的 Fork，Step 4 让外层仓库记录新的 commit SHA。两步都必须执行！

### 2. 同步官方最新代码

```powershell
# Step 1: 进入子模块
cd netbox

# Step 2: 拉取官方最新并合并
git fetch upstream
git merge upstream/main

# Step 3: 如有冲突，手动解决后提交
git add .
git commit -m "合并上游更新"
git push origin main

# Step 4: 回外层锁定版本
cd ..
git add netbox
git commit -m "同步 netbox 上游最新版本"
git push
```

### 3. 查看当前子模块版本

```powershell
git submodule status
```

---

## 操作流程图

```
┌─ 修改代码 ─────────────────────────────┐
│  cd netbox                              │
│  改代码 → git add → git commit         │
│  git push origin main                   │
│  cd .. → git add netbox → git commit   │
│  git push                               │
└─────────────────────────────────────────┘

┌─ 同步官方 ─────────────────────────────┐
│  cd netbox                              │
│  git fetch upstream                     │
│  git merge upstream/main                │
│  解决冲突(如有) → git push origin main  │
│  cd .. → git add netbox → git commit   │
│  git push                               │
└─────────────────────────────────────────┘
```

---

## Remote 配置说明

| 名称 | 地址 | 用途 |
|------|------|------|
| `origin` | `https://github.com/<你的用户名>/netbox.git` | 推送你的修改 |
| `upstream` | `https://github.com/netbox-community/netbox.git` | 拉取官方更新 |

验证配置：
```powershell
cd netbox
git remote -v
```

---

## 常见问题

### Q: 忘记在外层 git add netbox 会怎样？
A: 其他人拉取外层仓库后，子模块仍然指向旧版本 commit，看不到你的最新修改。

### Q: 合并上游时冲突怎么办？
A: 手动解决冲突文件 → `git add .` → `git commit` → `git push origin main` → 回外层锁定版本。

### Q: 如何回退子模块到某个历史版本？
```powershell
cd netbox
git checkout <commit-SHA>
cd ..
git add netbox
git commit -m "回退 netbox 到指定版本"
```
