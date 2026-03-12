# 代码规范快速参考

精简版规范，包含最常用的规则，适合日常快速查阅。

---

## 0. 核心原则

### 规范优先原则
**所有代码必须严格遵循当前规范标准，无需考虑向后兼容性或遗留系统支持，仅针对目标环境实现最优解决方案。**

- 以当前规范为唯一标准，不妥协于历史代码风格
- 充分利用目标环境的最新特性，不迁就旧版本兼容性
- 优先实现最优方案，不因遗留系统而降低代码质量

```javascript
// ✅ 好的示例 - 使用最新特性
const result = data?.map(item => item.value).filter(Boolean) ?? [];

// ❌ 避免 - 为了兼容旧代码而使用过时写法
var result = data && data.map ? data.map(function(item) { return item.value; }) : [];
```

---

## 1. 命名规范

| 类型 | 风格 | 示例 |
|------|------|------|
| 变量 | 小驼峰 | `userName`, `totalCount` |
| 常量 | 全大写下划线 | `MAX_SIZE`, `API_BASE_URL` |
| 函数/方法 | 小驼峰 | `getUserInfo()`, `calculateTotal()` |
| 类/接口 | 大驼峰 | `UserService`, `OrderRepository` |
| 文件/目录 | 小写连字符 | `user-service.ts`, `api-client/` |

---

## 2. 代码格式

### 2.1 基本规则
- 使用 **2 或 4 个空格** 缩进（根据语言惯例）
- 每行不超过 **80-120** 个字符
- 运算符前后加空格：`a + b` ✓ | `a+b` ✗
- 文件末尾保留一个空行

### 2.2 大括号风格（K&R）
```javascript
// ✓ 推荐
if (condition) {
    doSomething();
}

// ✗ 避免
if (condition)
    doSomething();
```

---

## 3. 注释规范

### 3.1 原则
- 注释解释 **"为什么"**，而非 **"是什么"**
- 保持注释简洁明了
- 及时更新过时的注释

### 3.2 文档注释模板
```javascript
/**
 * 函数描述
 * @param {string} name - 参数说明
 * @returns {Object} 返回值说明
 * @throws {Error} 异常说明
 */
```

---

## 4. 代码组织

### 4.1 导入顺序
1. 标准库/框架导入
2. 第三方库导入
3. 内部模块导入
4. 相对路径导入
5. 样式/资源导入

### 4.2 文件结构
```javascript
// 1. 常量定义
const MAX_RETRY = 3;

// 2. 类型/接口定义
interface UserConfig {
  name: string;
}

// 3. 类/函数定义
class UserService {
  // 属性 → 构造函数 → 公共方法 → 私有方法
}

// 4. 导出
export { UserService };
```

---

## 5. 错误处理

```javascript
// ✓ 好的示例
try {
  const data = await fetchUserData(userId);
  processUserData(data);
} catch (error) {
  logger.error(`Failed to fetch user data:`, error);
  throw new UserDataFetchError(`无法获取用户数据`);
}

// ✗ 避免
try {
  fetchUserData(userId);
} catch (e) {
  // 忽略错误
}
```

---

## 6. Git 提交规范

### 6.1 格式
```
<type>(<scope>): <subject>

<body>

<footer>
```

### 6.2 常用类型
| 类型 | 说明 |
|------|------|
| `feat` | 新功能 |
| `fix` | 修复 bug |
| `docs` | 文档更新 |
| `style` | 代码格式 |
| `refactor` | 重构 |
| `test` | 测试相关 |
| `chore` | 构建/工具变动 |

### 6.3 示例
```
feat(user): 添加用户登录功能

fix(api): 修复空指针异常

docs: 更新 API 文档
```

---

## 7. 语言特定速查

### JavaScript/TypeScript
- 优先使用 **TypeScript**
- 使用 `const` / `let`，避免 `var`
- 优先使用 `async/await` 替代回调
- 使用严格相等 `===` / `!==`

### Java
- 使用 4 个空格缩进
- 类名大驼峰，方法名小驼峰
- 使用 `Optional` 避免空指针
- 优先使用 Stream API

### Python
- 使用 4 个空格缩进
- 遵循 PEP 8
- 使用 f-string 格式化字符串
- 使用类型注解

---

## 8. 硬编码规范

### 8.1 必须使用常量的场景
- 业务状态值（用户状态、订单状态等）
- 配置参数（超时时间、重试次数等）
- 外部接口标识（API 地址、错误码等）
- 多处使用的值
- 可能变化的值

### 8.2 可以直接使用字面量的场景
- 语义明确（`count > 0`, `index + 1`）
- 局部使用（循环计数器、临时计算）
- 标准值（`Math.PI`, `100` 百分比）
- 测试数据

### 8.3 需要注释的场景
```javascript
// ✅ 添加注释说明
const maxRetries = 3; // 网络请求失败后的最大重试次数
const result = value * 0.15; // 15% 的服务费
```

---

## 9. 代码审查清单

- [ ] 代码可以编译/运行
- [ ] 命名清晰有意义
- [ ] 注释解释"为什么"
- [ ] 错误处理完善
- [ ] 业务状态值使用常量
- [ ] 配置参数使用常量
- [ ] 语义不明确的字面量有注释
- [ ] 测试覆盖核心逻辑
- [ ] 提交信息符合规范

---

## 参考文档

- 详细规范：[general-coding-standards.md](./general-coding-standards.md)
- JS/TS 规范：[../languages/javascript-typescript-standards.md](../languages/javascript-typescript-standards.md)
- Java 规范：[../languages/java-standards.md](../languages/java-standards.md)
- Python 规范：[../languages/python-standards.md](../languages/python-standards.md)
- Git 规范：[../practices/git-commit-standards.md](../practices/git-commit-standards.md)
- 项目结构：[../practices/project-structure-standards.md](../practices/project-structure-standards.md)

---

*最后更新：2026-03-12*
