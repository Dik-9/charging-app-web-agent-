# 🏢 企业级 Git 工作流程指南

## 1. 分支管理策略

### 主要分支类型

| 分支名称 | 用途 | 是否永久 |
|---------|------|---------|
| `master` | 主分支，存放稳定的生产代码 | ✅ 是 |
| `feature/*` | 功能开发分支 | ❌ 否 |
| `bugfix/*` | Bug 修复分支 | ❌ 否 |
| `hotfix/*` | 紧急修复分支 | ❌ 否 |

## 2. 提交信息规范

### 格式要求
```
<类型>(<模块>): <简短描述>

[详细说明（可选）]
```

### 类型说明
| 类型 | 说明 | 示例 |
|------|------|------|
| `feat` | 新增功能 | `feat(首页): 添加轮播图` |
| `fix` | 修复 bug | `fix(支付): 修复支付失败问题` |
| `docs` | 文档更新 | `docs(README): 更新安装说明` |
| `style` | 代码格式调整 | `style(登录页): 调整按钮样式` |
| `refactor` | 代码重构 | `refactor(utils): 优化工具函数` |
| `test` | 测试相关 | `test(用户): 添加登录测试用例` |
| `chore` | 其他维护工作 | `chore(deps): 更新依赖` |

### ✅ 正确示例
```
feat(首页): 添加轮播图组件

- 实现自动播放功能
- 添加指示器
- 支持手动滑动
```

### ❌ 错误示例
- ❌ `修改了首页`
- ❌ `fix bug`
- ❌ `update file`

## 3. 开发流程

### 步骤 1: 更新本地代码
```bash
git checkout master
git pull origin master
```

### 步骤 2: 创建功能分支
```bash
git checkout -b feature/add-login-page
```

### 步骤 3: 开发和提交
```bash
# 查看状态
git status

# 添加文件
git add .

# 提交（必须遵循规范）
git commit -m "feat(登录): 实现登录页面"
```

### 步骤 4: 推送到远程
```bash
git push origin feature/add-login-page
```

### 步骤 5: 创建 Pull Request
在 GitHub 上创建 PR，等待代码审查

### 步骤 6: 合并到 master
审查通过后，合并到 master 分支

## 4. 团队协作规范

### 代码审查要求
- ✅ 每个 PR 至少需要 1 位同事审查
- ✅ 审查通过后才能合并
- ✅ 禁止直接推送到 master

### 冲突解决
- 定期拉取 master 代码
- 在本地解决冲突后再推送

## 5. 常用命令速查表

| 操作 | 命令 |
|------|------|
| 查看分支 | `git branch -a` |
| 切换分支 | `git checkout 分支名` |
| 创建分支 | `git checkout -b 分支名` |
| 删除本地分支 | `git branch -d 分支名` |
| 删除远程分支 | `git push origin --delete 分支名` |
| 查看提交历史 | `git log --oneline` |
| 撤销修改 | `git checkout -- 文件路径` |

---

📝 **提示**: 每次提交前，请确保代码可以正常运行！