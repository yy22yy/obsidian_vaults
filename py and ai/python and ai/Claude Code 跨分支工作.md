# Claude Code 跨分支工作详解

> Claude Code 的对话与文件夹绑定，而非 Git 分支，带来无缝的跨分支协作体验

---

## 核心特性

### 对话与分支的关系

| 特性         | 说明                            |
| ---------- | ----------------------------- |
| 对话绑定文件夹    | Claude Code 的对话记录与当前工作目录绑定    |
| 切换分支不丢失上下文 | `git checkout` 切换分支后，聊天记录保持不变 |
| 自动感知文件变化   | Claude 自动看到新分支的文件内容           |
| 分支间上下文共享   | 同一文件夹下的不同分支共用对话历史             |

---

## 实际场景示例

```
场景：你在 main 分支讨论了一个 bug 修复方案

1. git checkout feature/fix-bug    # 切换到修复分支
2. Claude 仍然记得之前讨论的方案   # 上下文不丢失
3. Claude 能看到新分支的文件       # 自动感知变化
4. 实现修复方案                    # 无缝继续工作
```

---

## 进阶：Git Worktrees 多分支并行开发

### 什么是 Git Worktrees？

Git Worktrees 允许你将**同一个仓库的不同分支**同时检出到**不同目录**，实现：

- 多个分支同时存在，无需频繁切换
- 每个分支独立的工作目录
- Claude Code 对话完全隔离，互不干扰

### Worktree 与普通切换的区别

| 方式 | 目录数 | Claude 对话 | 适用场景 |
|------|--------|-------------|----------|
| git checkout | 1个 | 共用历史 | 单任务顺序开发 |
| git worktree | 多个 | 完全隔离 | 多任务并行开发 |

---

## Git Worktrees 使用方法

### 创建 Worktree

```bash
# 基于现有分支创建 worktree
git worktree add ../project-feature feature-branch

# 基于新分支创建 worktree
git worktree add ../project-new -b new-branch

# 创建到指定路径
git worktree add /path/to/directory branch-name
```

### 查看 Worktree 列表

```bash
git worktree list
```

输出示例：
```
main        /home/user/project
feature     /home/user/project-feature
bugfix      /home/user/project-bugfix
```

### 删除 Worktree

```bash
# 删除 worktree（分支保留）
git worktree remove ../project-feature

# 删除 worktree 和分支
git worktree remove ../project-feature --force
git branch -d feature-branch
```

### Claude Code 自动创建 Worktree

Claude Code 提供 `EnterWorktree` 工具，可以自动创建临时 worktree：

```
用途：在隔离环境中执行任务，完成后自动清理
适用：测试、实验性修改、并行任务
```

---

## 多分支并行开发 workflow

```
项目目录结构：

project/                  # main 分支
project-feature-a/        # feature-a 分支 (worktree)
project-feature-b/        # feature-b 分支 (worktree)
project-hotfix/           # hotfix 分支 (worktree)
```

**操作流程：**

1. 为每个任务创建 worktree
2. 在每个目录启动独立的 Claude Code 会话
3. 各会话独立工作，互不干扰
4. 任务完成后合并分支，删除 worktree

---

## 最佳实践

### 单分支顺序开发
- 直接使用 `git checkout` 切换
- Claude 记住所有历史讨论
- 适合：修复 bug → 测试 → 提交 → 切回 main

### 多分支并行开发
- 使用 `git worktree` 创建多个目录
- 每个目录启动 Claude Code
- 适合：
  - 同时开发多个 feature
  - 一边写代码一边写文档
  - hotfix 与 feature 同时进行

### Claude Code Worktree 模式
- 使用 `EnterWorktree` 工具
- 自动创建隔离环境
- 任务完成后自动清理
- 适合：临时测试、代码审查

---

## 注意事项

1. **Worktree 会占用磁盘空间** - 每个目录都是完整的文件副本
2. **不要在同一 worktree 中再次创建 worktree** - 会造成混乱
3. **删除 worktree 前确保已提交或合并** - 避免丢失工作
4. **Claude Code 记忆按目录隔离** - worktree 间不共享记忆

---

## 相关链接

- [[Claude Code]] - Claude Code 简介
- [[claude 目录功能详解]] - .claude 目录结构
- Git Worktrees 官方文档：https://git-scm.com/docs/git-worktree