# Git 提交规范指南

## 概述

本文档定义了团队的 Git 提交规范，旨在保持清晰的提交历史，便于代码审查和问题追溯。

## Commit Message 格式

### 基本结构

```
<type>(<scope>): <subject>

<body>

<footer>
```

### 各部分说明

#### 1. Type（必需）

提交的类型，必须是以下之一：

| Type | 说明 | 示例 |
|------|------|------|
| `feat` | 新功能 | feat(user): 添加用户头像上传功能 |
| `fix` | 修复 bug | fix(order): 修复订单金额计算错误 |
| `docs` | 文档变更 | docs(readme): 更新安装说明 |
| `style` | 代码格式调整（不影响代码逻辑） | style(user): 格式化用户服务代码 |
| `refactor` | 重构（既不是新功能也不是修复 bug） | refactor(auth): 重构认证模块 |
| `perf` | 性能优化 | perf(query): 优化用户查询 SQL |
| `test` | 测试相关 | test(user): 添加用户服务单元测试 |
| `build` | 构建系统或外部依赖变更 | build(maven): 升级 Spring Boot 到 3.0 |
| `ci` | CI/CD 配置变更 | ci(jenkins): 更新构建流水线配置 |
| `chore` | 其他不修改 src 或 test 的变更 | chore(deps): 更新依赖版本 |
| `revert` | 回滚之前的 commit | revert: 回滚 feat(user): 添加用户头像上传功能 |

#### 2. Scope（可选）

提交影响的范围，通常是模块名或功能名：

- `user` - 用户模块
- `order` - 订单模块
- `payment` - 支付模块
- `auth` - 认证模块
- `config` - 配置相关
- `db` - 数据库相关

示例：
```
feat(user): 添加用户注册功能
fix(order): 修复订单状态更新问题
```

#### 3. Subject（必需）

简短描述，不超过 50 个字符：

- 使用中文描述
- 使用祈使句，例如"添加"而不是"添加了"
- 首字母小写
- 结尾不加句号

**推荐：**
```
feat(user): 添加用户登录功能
fix(order): 修复订单金额精度问题
```

**不推荐：**
```
feat(user): 添加了用户登录功能。
fix(order): 订单金额精度问题已修复
```

#### 4. Body（可选）

详细描述本次提交的内容：

- 与 subject 空一行
- 说明修改的原因、之前的行为、现在的行为
- 可以分多行
- 每行不超过 72 个字符

示例：
```
feat(user): 添加用户注册邮箱验证功能

- 新增邮件发送服务
- 添加验证码生成和校验逻辑
- 完善用户注册流程，注册后需要邮箱验证才能激活账号
- 添加验证码 5 分钟过期机制

邮箱验证码使用 Redis 存储，避免数据库频繁读写
```

#### 5. Footer（可选）

主要用于以下情况：

**关联 Issue：**
```
Closes #123
Fixes #456
Refs #789
```

**Breaking Changes（重大变更）：**
```
BREAKING CHANGE:
用户登录接口返回格式变更，token 字段从 accessToken 改为 access_token
```

## 完整示例

### 示例 1：新功能

```
feat(user): 添加用户实名认证功能

- 新增实名认证接口
- 接入第三方实名认证服务
- 添加认证状态字段到用户表
- 实现认证状态查询接口

实名认证使用阿里云服务，认证信息加密存储

Closes #234
```

### 示例 2：Bug 修复

```
fix(order): 修复并发下单时库存扣减不准确的问题

问题原因：原有实现使用普通 UPDATE 语句扣减库存，在高并发场景下
存在超卖风险

解决方案：使用 UPDATE ... WHERE stock >= quantity 的方式进行库存
扣减，利用数据库行锁保证库存扣减的原子性

Fixes #567
```

### 示例 3：性能优化

```
perf(product): 优化商品列表查询性能

- 添加商品分类和状态的联合索引
- 优化 SQL 查询语句，避免 SELECT *
- 添加 Redis 缓存，缓存时间 5 分钟
- 查询结果使用 DTO 投影，减少数据传输量

优化后查询时间从平均 800ms 降低到 50ms

Refs #890
```

### 示例 4：重构

```
refactor(auth): 重构认证模块，使用 Spring Security

- 移除自定义的认证拦截器
- 使用 Spring Security 实现认证和授权
- 统一异常处理
- 简化配置

重构后代码更清晰，安全性更高，便于后续扩展

BREAKING CHANGE:
认证方式从自定义 token 改为 JWT，客户端需要相应调整
```

### 示例 5：文档更新

```
docs(api): 更新 API 接口文档

- 补充用户模块接口说明
- 添加请求和响应示例
- 更新错误码说明
- 修正部分接口参数错误
```

## 提交规范最佳实践

### 1. 提交频率

- **小步提交**: 每完成一个小功能或修复一个问题就提交，不要累积大量修改
- **功能完整**: 每次提交应该是一个完整的功能点或修复，能够独立运行
- **编译通过**: 提交前确保代码能够编译通过，不要提交有编译错误的代码

### 2. 提交内容

- **单一职责**: 一次提交只做一件事，不要在一个 commit 中混合多个功能
- **相关性**: 提交的文件应该是相关的，不要包含无关的修改
- **完整性**: 提交应该包含实现功能所需的所有文件（代码、配置、测试等）

**推荐：**
```bash
# 功能 1
git commit -m "feat(user): 添加用户注册功能"

# 功能 2
git commit -m "feat(user): 添加用户登录功能"
```

**不推荐：**
```bash
# 混合多个功能
git commit -m "feat(user): 添加用户注册和登录功能，修复订单bug，优化查询性能"
```

### 3. 代码审查

提交前自我审查：

```bash
# 查看修改内容
git diff

# 查看暂存区内容
git diff --staged

# 分块提交
git add -p
```

### 4. 分支管理

- `main/master`: 主分支，始终保持可发布状态
- `develop`: 开发分支，日常开发在此分支
- `feature/*`: 功能分支，开发新功能
- `bugfix/*`: bug 修复分支
- `hotfix/*`: 紧急修复分支
- `release/*`: 发布分支

示例：
```bash
# 创建功能分支
git checkout -b feature/user-register develop

# 开发完成后合并回 develop
git checkout develop
git merge --no-ff feature/user-register
git branch -d feature/user-register
```

## Git 工作流程

### 日常开发流程

```bash
# 1. 更新代码
git checkout develop
git pull origin develop

# 2. 创建功能分支
git checkout -b feature/user-profile

# 3. 开发并提交
git add .
git commit -m "feat(user): 添加用户个人资料页面"

# 4. 推送到远程
git push origin feature/user-profile

# 5. 创建 Pull Request/Merge Request
# 在 GitLab/GitHub 上创建 MR/PR

# 6. 代码审查通过后合并
# 删除本地和远程分支
git checkout develop
git branch -d feature/user-profile
git push origin --delete feature/user-profile
```

### Bug 修复流程

```bash
# 1. 基于 develop 创建 bugfix 分支
git checkout -b bugfix/order-amount-calculation develop

# 2. 修复并提交
git add .
git commit -m "fix(order): 修复订单金额计算精度问题"

# 3. 推送并创建 MR/PR
git push origin bugfix/order-amount-calculation
```

### 紧急修复流程（Hotfix）

```bash
# 1. 基于 main 创建 hotfix 分支
git checkout -b hotfix/1.0.1 main

# 2. 修复问题
git commit -m "fix(security): 修复 SQL 注入漏洞"

# 3. 合并到 main 和 develop
git checkout main
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "紧急修复版本 1.0.1"

git checkout develop
git merge --no-ff hotfix/1.0.1

# 4. 删除 hotfix 分支
git branch -d hotfix/1.0.1
```

## 常见问题

### 1. 提交了错误的内容怎么办？

```bash
# 修改最后一次提交（未推送）
git commit --amend

# 撤销最后一次提交（保留修改）
git reset --soft HEAD^

# 撤销最后一次提交（不保留修改）
git reset --hard HEAD^
```

### 2. 如何合并多个提交？

```bash
# 交互式 rebase，合并最近 3 次提交
git rebase -i HEAD~3

# 在编辑器中将需要合并的 commit 标记为 squash 或 s
```

### 3. 如何编写 Revert 提交？

```bash
# 自动生成 revert 提交
git revert <commit-hash>

# 提交信息格式
revert: feat(user): 添加用户注册功能

This reverts commit abc123def456.

原因：该功能存在安全隐患，暂时撤回
```

## 工具推荐

### Commitlint

自动检查 commit message 是否符合规范：

```bash
# 安装
npm install --save-dev @commitlint/cli @commitlint/config-conventional

# 配置 commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'docs', 'style', 'refactor',
      'perf', 'test', 'build', 'ci', 'chore', 'revert'
    ]],
    'subject-max-length': [2, 'always', 50]
  }
};
```

### Husky

Git hooks 工具，在提交前自动检查：

```bash
# 安装
npm install --save-dev husky

# 配置 pre-commit hook
npx husky add .husky/commit-msg 'npx --no-install commitlint --edit "$1"'
```

## 参考资料

- [Conventional Commits](https://www.conventionalcommits.org/)
- [Angular Commit Message Guidelines](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit)
- [Git Flow 工作流](https://nvie.com/posts/a-successful-git-branching-model/)

---

遵循以上规范，让团队的 Git 提交历史更清晰、更易维护！
