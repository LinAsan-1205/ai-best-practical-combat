# Git 提交规范

## 1. 提交信息格式

### 1.1 基本格式
```
<type>(<scope>): <subject>

<body>

<footer>
```

### 1.2 格式说明
- **type**（必需）: 提交类型
- **scope**（可选）: 影响范围
- **subject**（必需）: 简短描述
- **body**（可选）: 详细描述
- **footer**（可选）: 脚注信息

## 2. 提交类型 (Type)

| 类型 | 说明 | 示例 |
|------|------|------|
| `feat` | 新功能 | `feat(user): 添加用户登录功能` |
| `fix` | 修复 bug | `fix(api): 修复空指针异常` |
| `docs` | 文档更新 | `docs(readme): 更新安装说明` |
| `style` | 代码格式（不影响代码运行） | `style: 格式化代码，删除多余空格` |
| `refactor` | 重构（既不是新功能也不是修复） | `refactor(service): 重构用户服务` |
| `perf` | 性能优化 | `perf(query): 优化数据库查询` |
| `test` | 测试相关 | `test(unit): 添加用户服务单元测试` |
| `chore` | 构建过程或辅助工具的变动 | `chore(deps): 升级依赖版本` |
| `ci` | CI/CD 相关 | `ci(github): 添加自动化测试` |
| `revert` | 回滚提交 | `revert: 回滚 feat(user) 提交` |

## 3. 影响范围 (Scope)

### 3.1 常见范围
- `api` - API 接口
- `ui` - 用户界面
- `db` - 数据库
- `auth` - 认证授权
- `config` - 配置
- `deps` - 依赖
- `docs` - 文档
- `test` - 测试
- `build` - 构建
- `ci` - CI/CD

### 3.2 范围示例
```
feat(api): 添加用户注册接口
fix(ui): 修复按钮样式问题
docs(readme): 更新项目说明
refactor(service): 重构订单服务
```

## 4. 主题 (Subject)

### 4.1 主题规范
- 使用祈使句，现在时态（"change" 而非 "changed" 或 "changes"）
- 首字母小写
- 结尾不加句号
- 不超过 50 个字符
- 清晰描述变更内容

### 4.2 好的示例
```
feat(user): 添加用户登录功能
fix(api): 修复空指针异常
docs: 更新 API 文档
```

### 4.3 避免的示例
```
feat(user): Added user login feature  # 使用过去时
feat(user): 添加用户登录功能。        # 结尾有句号
feat(user): 添加用户登录功能和注册功能和密码重置功能  # 太长
fix: fix bug                          # 太模糊
```

## 5. 正文 (Body)

### 5.1 正文规范
- 使用祈使句，现在时态
- 说明变更的动机和与之前行为的对比
- 每行不超过 72 个字符
- 与主题之间空一行

### 5.2 正文示例
```
feat(user): 添加用户登录功能

添加基于 JWT 的用户认证系统，包括：
- 用户名/密码登录
- Token 刷新机制
- 登录状态验证

与之前的 Session 方案相比，JWT 更适合分布式部署。
```

## 6. 脚注 (Footer)

### 6.1 常用脚注
- **Breaking Changes**: 破坏性变更说明
- **Closes**: 关闭的 Issue
- **Refs**: 相关的 Issue 或 PR

### 6.2 脚注示例
```
feat(api): 修改用户接口返回格式

BREAKING CHANGE: 用户接口返回格式从嵌套对象改为扁平结构，
需要前端同步更新。

Closes: #123
Refs: #456, #789
```

## 7. 完整示例

### 7.1 简单提交
```
feat(user): 添加用户注册功能
```

### 7.2 详细提交
```
feat(auth): 实现基于 JWT 的认证系统

添加完整的 JWT 认证流程：
- 用户登录时生成 access token 和 refresh token
- 实现 token 自动刷新机制
- 添加 token 黑名单用于登出

性能优化：
- 使用 Redis 缓存 token 信息
- 减少数据库查询次数

Closes: #234
```

### 7.3 破坏性变更
```
feat(api): 统一 API 响应格式

将所有 API 响应格式统一为：
{
  "code": 200,
  "data": {...},
  "message": "success"
}

BREAKING CHANGE: 响应格式从直接返回数据改为包装对象，
需要所有调用方更新代码。

Closes: #345
```

## 8. 分支管理

### 8.1 分支命名
| 分支类型 | 命名格式 | 示例 |
|----------|----------|------|
| 主分支 | `main` / `master` | `main` |
| 开发分支 | `develop` | `develop` |
| 功能分支 | `feature/<描述>` | `feature/user-login` |
| 修复分支 | `fix/<描述>` | `fix/null-pointer` |
| 发布分支 | `release/<版本>` | `release/v1.2.0` |
| 热修复分支 | `hotfix/<描述>` | `hotfix/critical-bug` |

### 8.2 分支策略（Git Flow）
```
main (生产环境)
  ↑
develop (开发环境)
  ↑    ↑
feature/*  fix/*
```

## 9. 提交频率

### 9.1 提交原则
- 每个提交只做一件事
- 保持提交的原子性
- 及时提交，避免大量未提交代码
- 提交前确保代码可以编译/运行

### 9.2 好的实践
```bash
# 完成一个功能点后提交
git add src/auth/login.ts
git commit -m "feat(auth): 实现用户登录功能"

# 修复一个 bug 后提交
git add src/utils/validator.ts
git commit -m "fix(validator): 修复邮箱验证正则表达式"

# 添加测试后提交
git add tests/auth/
git commit -m "test(auth): 添加登录功能单元测试"
```

## 10. 提交前检查

### 10.1 检查清单
- [ ] 代码可以编译/运行
- [ ] 通过了所有测试
- [ ] 代码风格符合规范
- [ ] 没有遗留调试代码
- [ ] 提交信息符合规范

### 10.2 使用 Git Hooks
```bash
# .git/hooks/pre-commit
#!/bin/sh
# 运行代码检查
npm run lint

# 运行测试
npm run test:unit

# 检查提交信息
commit_msg=$(cat $1)
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|style|refactor|perf|test|chore|ci|revert)(\(.+\))?: .+"; then
    echo "提交信息格式错误！"
    echo "格式: <type>(<scope>): <subject>"
    exit 1
fi
```

## 11. 版本标签

### 11.1 语义化版本
格式：`v主版本.次版本.修订号`

- **主版本**：不兼容的 API 修改
- **次版本**：向下兼容的功能新增
- **修订号**：向下兼容的问题修复

### 11.2 标签示例
```bash
# 创建标签
git tag -a v1.2.0 -m "Release version 1.2.0"

# 推送标签到远程
git push origin v1.2.0

# 推送所有标签
git push origin --tags
```

## 12. 常用命令

### 12.1 提交相关
```bash
# 查看提交历史
git log --oneline --graph

# 修改最后一次提交
git commit --amend

# 撤销最后一次提交（保留修改）
git reset --soft HEAD~1

# 查看提交详情
git show <commit-hash>

# 比较两个提交
git diff <commit1> <commit2>
```

### 12.2 分支相关
```bash
# 创建并切换到新分支
git checkout -b feature/new-feature

# 合并分支
git checkout main
git merge feature/new-feature

# 删除本地分支
git branch -d feature/new-feature

# 删除远程分支
git push origin --delete feature/new-feature
```

## 13. 工具推荐

### 13.1 提交信息工具
- **commitlint**: 检查提交信息格式
- **commitizen**: 交互式提交工具
- **husky**: Git hooks 管理
- **lint-staged**: 暂存文件检查

### 13.2 配置示例
```json
// .commitlintrc.json
{
  "extends": ["@commitlint/config-conventional"],
  "rules": {
    "type-enum": [
      2,
      "always",
      ["feat", "fix", "docs", "style", "refactor", "perf", "test", "chore", "ci", "revert"]
    ],
    "subject-full-stop": [2, "never", "."],
    "subject-case": [2, "never", ["sentence-case", "start-case", "pascal-case", "upper-case"]]
  }
}
```
