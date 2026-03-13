# Git 高效使用技巧

Git 是目前最流行的分布式版本控制系统，掌握一些高效技巧可以大大提升开发效率。

## 基础配置

### 用户信息

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### 别名配置

```bash
# 简化常用命令
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status

# 查看日志树形结构
git config --global alias.lg "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

## 常用技巧

### 1. 修改最后一次提交

```bash
# 添加遗漏的文件
git add forgotten-file.txt

# 修改最后一次提交（不改变提交信息）
git commit --amend --no-edit

# 修改最后一次提交信息
git commit --amend
```

### 2. 暂存工作区

```bash
# 临时保存当前工作
git stash

# 查看暂存列表
git stash list

# 恢复暂存并删除
git stash pop

# 恢复暂存但不删除
git stash apply

# 命名暂存
git stash save "work in progress"
```

### 3. 撤销操作

```bash
# 撤销工作区的修改
git checkout -- file.txt

# 撤销暂存区的修改
git reset HEAD file.txt

# 回退到指定版本
git reset --hard <commit-id>

# 撤销某次提交（创建新提交）
git revert <commit-id>
```

### 4. 交互式变基

```bash
# 修改最近 3 次提交
git rebase -i HEAD~3

# 在编辑器中，将要修改的提交行的 pick 改为：
# - edit: 修改该提交
# - squash: 合并到上一个提交
# - drop: 删除该提交
```

### 5. 查找提交

```bash
# 在提交历史中搜索
git log --all --grep="keyword"

# 查找引入 bug 的提交
git bisect start
git bisect bad
git bisect good <good-commit-id>
```

### 6. 分支管理

```bash
# 创建并切换分支
git checkout -b feature-branch

# 重命名分支
git branch -m old-name new-name

# 删除本地分支
git branch -d branch-name

# 删除远程分支
git push origin --delete branch-name

# 查看所有分支
git branch -a
```

### 7. 比较差异

```bash
# 比较工作区和暂存区
git diff

# 比较暂存区和上一次提交
git diff --cached

# 比较两个分支
git diff branch1 branch2

# 比较两个提交
git diff commit1 commit2
```

### 8. 清理

```bash
# 清理未跟踪的文件
git clean -f

# 清理未跟踪的文件和目录
git clean -fd

# 清理未跟踪和被忽略的文件
git clean -fdX
```

## 最佳实践

### 提交规范

使用 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

```
<type>(<scope>): <subject>

<body>

<footer>
```

- **type**: feat, fix, docs, style, refactor, test, chore
- **scope**: 影响范围
- **subject**: 简短描述
- **body**: 详细说明
- **footer**: 破坏性变更说明

### 示例

```bash
# 功能
git commit -m "feat(auth): add user login feature"

# 修复
git commit -m "fix(user): resolve login validation error"

# 文档
git commit -m "docs(readme): update installation guide"
```

### 工作流

1. 从主分支创建功能分支
2. 开发并提交代码
3. 推送到远程
4. 创建 Pull Request
5. 代码审查
6. 合并到主分支
7. 删除功能分支

## 常用命令速查

| 命令 | 说明 |
|------|------|
| `git status` | 查看状态 |
| `git add .` | 添加所有修改 |
| `git commit -m "msg"` | 提交 |
| `git push` | 推送 |
| `git pull` | 拉取 |
| `git branch` | 查看分支 |
| `git checkout -b` | 创建分支 |
| `git merge` | 合并分支 |
| `git log --oneline` | 简洁日志 |
| `git reflog` | 操作记录 |

## 总结

掌握 Git 的高级技巧可以让你更高效地管理代码版本，避免常见的坑。建议结合实际项目多练习，加深理解。

## 参考资料

- [Git 官方文档](https://git-scm.com/doc)
- [Pro Git 中文版](https://git-scm.com/book/zh/v2)

---

*最后更新时间: {docsify-updated}*
