# 通用代码规范

## 0. 核心原则

### 0.1 规范优先原则
**所有代码必须严格遵循当前规范标准，无需考虑向后兼容性或遗留系统支持，仅针对目标环境实现最优解决方案。**

- 以当前规范为唯一标准，不妥协于历史代码风格
- 充分利用目标环境的最新特性，不迁就旧版本兼容性
- 优先实现最优方案，不因遗留系统而降低代码质量
- 新代码必须 100% 符合规范，不对旧代码做兼容适配

### 0.2 示例
```javascript
// ✅ 好的示例 - 使用最新特性，不考虑旧浏览器兼容
const result = data?.map(item => item.value).filter(Boolean) ?? [];

// ✅ 好的示例 - 使用目标环境原生 API，不引入 polyfill
const unique = [...new Set(items)];

// ❌ 避免 - 为了兼容旧代码而使用过时写法
var result = data && data.map ? data.map(function(item) { return item.value; }) : [];
```

## 1. 命名规范

### 1.1 通用原则
- 使用有意义的、描述性的名称
- 避免使用缩写，除非是广泛认可的（如 `id`, `url`, `api`）
- 避免使用单字母变量名（循环索引除外）
- 保持命名一致性

### 1.2 命名风格
| 类型 | 风格 | 示例 |
|------|------|------|
| 变量 | 小驼峰 | `userName`, `totalCount` |
| 常量 | 全大写下划线 | `MAX_SIZE`, `API_BASE_URL` |
| 函数/方法 | 小驼峰 | `getUserInfo()`, `calculateTotal()` |
| 类/接口 | 大驼峰 | `UserService`, `OrderRepository` |
| 文件/目录 | 小写连字符 | `user-service.ts`, `api-client/` |
| 数据库表 | 小写下划线 | `user_accounts`, `order_items` |
| 数据库字段 | 小写下划线 | `created_at`, `updated_by` |

## 2. 代码格式

### 2.1 缩进与空格
- 使用 2 或 4 个空格缩进（根据语言惯例）
- 不使用 Tab 字符
- 运算符前后加空格：`a + b` 而非 `a+b`
- 逗号后加空格
- 冒号后加空格（对象字面量等）

### 2.2 换行
- 每行不超过 80-120 个字符
- 长表达式在运算符后换行
- 链式调用每个方法一行

```javascript
// 好的示例
const result = someVeryLongVariableName
  .doSomething()
  .doAnotherThing()
  .getResult();

// 避免
const result = someVeryLongVariableName.doSomething().doAnotherThing().getResult();
```

### 2.3 空行
- 文件末尾保留一个空行
- 逻辑相关的代码块之间用空行分隔
- 函数/方法之间用空行分隔

## 3. 注释规范

### 3.1 注释原则
- 注释解释"为什么"，而非"是什么"
- 保持注释简洁明了
- 及时更新过时的注释
- 避免冗余注释

### 3.2 注释类型
```javascript
// 单行注释：用于简短说明

/*
 * 多行注释：用于详细说明
 * 可以跨越多行
 */

/**
 * 文档注释：用于函数、类、接口的文档
 * @param {string} name - 参数说明
 * @returns {Object} 返回值说明
 */
```

## 4. 代码组织

### 4.1 文件组织
- 一个文件只包含一个主要概念（类、组件、模块）
- 相关文件放在同一目录
- 按功能而非类型组织目录

### 4.2 导入顺序
1. 标准库/框架导入
2. 第三方库导入
3. 内部模块导入
4. 相对路径导入
5. 样式/资源导入

### 4.3 代码结构
```javascript
// 1. 常量定义
const MAX_RETRY = 3;

// 2. 类型/接口定义（TypeScript）
interface UserConfig {
  name: string;
  age: number;
}

// 3. 类/函数定义
class UserService {
  // 属性
  private users: User[] = [];
  
  // 构造函数
  constructor() {}
  
  // 公共方法
  public getUser(id: string): User {}
  
  // 私有方法
  private validateUser(user: User): boolean {}
}

// 4. 导出
export { UserService };
```

## 5. 错误处理

### 5.1 基本原则
- 不要忽略错误
- 使用异常而非返回错误码
- 提供有意义的错误信息
- 在合适的层级处理错误

### 5.2 示例
```javascript
// 好的示例
try {
  const data = await fetchUserData(userId);
  processUserData(data);
} catch (error) {
  logger.error(`Failed to fetch user data for ${userId}:`, error);
  throw new UserDataFetchError(`无法获取用户 ${userId} 的数据`);
}

// 避免
try {
  fetchUserData(userId);
} catch (e) {
  // 忽略错误
}
```

## 6. 硬编码规范

### 6.1 必须使用常量的场景

| 场景 | 说明 | 示例 |
|------|------|------|
| **业务状态值** | 表示业务状态的字符串/数字 | 用户状态、订单状态、权限标识 |
| **配置参数** | 可调整的参数值 | 超时时间、重试次数、分页大小 |
| **外部接口** | 与外部系统交互的标识 | API 地址、错误码、响应消息 |
| **多处使用** | 在多个地方重复使用的值 | 默认值、边界值、魔法数字 |
| **可能变化** | 未来可能需要修改的值 | 版本号、限制值、阈值 |

```javascript
// ❌ 避免 - 业务状态值硬编码
if (status === 'active') {}
if (order.status === 2) {}  // 2 代表什么？

// ❌ 避免 - 配置参数硬编码
const timeout = 5000;  // 5秒？为什么是这个值？
const maxRetries = 3;  // 3次？可以调整吗？

// ✅ 好的示例 - 使用常量
import { USER_STATUS_ACTIVE, ORDER_STATUS_PAID } from '@/constants/status';
import { REQUEST_TIMEOUT_MS, MAX_RETRY_ATTEMPTS } from '@/constants/config';

if (status === USER_STATUS_ACTIVE) {}
if (order.status === ORDER_STATUS_PAID) {}
const timeout = REQUEST_TIMEOUT_MS;  // 配置集中管理
const maxRetries = MAX_RETRY_ATTEMPTS;  // 含义清晰，便于调整
```

### 6.2 可以直接使用字面量的场景

| 场景 | 说明 | 示例 |
|------|------|------|
| **语义明确** | 值本身就能说明含义 | `count > 0`, `index + 1` |
| **局部使用** | 只在当前上下文使用 | 循环计数器、临时计算 |
| **标准值** | 行业/语言标准值 | `Math.PI`, `100` (百分比) |
| **测试数据** | 测试用例中的数据 | mock 数据、测试输入 |

```javascript
// ✅ 可以直接使用 - 语义明确
if (count > 0) {}
const nextIndex = currentIndex + 1;
const percentage = (value / total) * 100;

// ✅ 可以直接使用 - 局部使用
for (let i = 0; i < items.length; i++) {}
const firstItem = items[0];
const lastItem = items[items.length - 1];

// ✅ 可以直接使用 - 标准值
const circumference = 2 * Math.PI * radius;
const isEmpty = str.length === 0;

// ✅ 可以直接使用 - 测试数据
expect(add(2, 3)).toBe(5);  // 测试数据直接使用
```

### 6.3 需要注释的场景

当使用字面量时，如果含义不够直观，应添加注释说明：

```javascript
// ✅ 添加注释说明
const maxRetries = 3; // 网络请求失败后的最大重试次数
const debounceDelay = 300; // 输入防抖延迟，单位毫秒

// ✅ 复杂计算添加注释
const cacheDuration = 60 * 60 * 1000; // 1小时的毫秒数

// ❌ 避免 - 魔法数字无注释
const result = value * 0.15;  // 0.15 是什么？

// ✅ 好的示例 - 添加注释或使用常量
const result = value * 0.15; // 15% 的服务费
// 或
const SERVICE_FEE_RATE = 0.15;
const result = value * SERVICE_FEE_RATE;
```

## 7. 性能考虑

- 避免过早优化
- 优先代码可读性
- 在性能关键路径上进行优化
- 使用性能分析工具定位瓶颈

## 8. 安全规范

- 永远不要信任用户输入
- 对所有输入进行验证和清理
- 使用参数化查询防止 SQL 注入
- 敏感信息不要硬编码
- 使用 HTTPS 进行网络通信

## 9. 测试规范

- 编写单元测试覆盖核心业务逻辑
- 测试命名清晰描述测试场景
- 使用 Given-When-Then 结构组织测试
- 保持测试独立，不相互依赖

```javascript
describe('UserService', () => {
  describe('getUser', () => {
    it('should return user when user exists', () => {
      // Given
      const userId = '123';
      
      // When
      const result = userService.getUser(userId);
      
      // Then
      expect(result).toBeDefined();
      expect(result.id).toBe(userId);
    });
  });
});
```
