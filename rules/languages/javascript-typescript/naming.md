## JavaScript / TypeScript 命名规范

本文件从原 `javascript-typescript-standards.md` 中拆分，只专注于「命名 & 文件尺寸」相关规则，作为 JS/TS 命名的唯一权威文档使用。

---

## 1. 总则

- **所有标识符必须语义化**：从名字就能看出用途和含义，而不是依赖注释或上下文猜测。
- **统一风格**：团队内所有项目统一遵循本规范，不允许个人风格。
- **可读性优先于简短**：多敲几个字比误解逻辑、浪费时间好得多。
- **与领域模型对齐**：命名尽量贴近业务词汇（领域驱动设计的“通用语言”）。

推荐的基础风格（与整体规范保持一致）：

- 变量 / 函数 / 方法：`camelCase`
- 类 / 接口 / 枚举 / 类型别名：`PascalCase`
- 常量：`UPPER_SNAKE_CASE`
- 文件 / 目录：`kebab-case`

---

## 2. 禁止使用 `is` 前缀

**所有变量、函数、方法、类和接口名称禁止以 `is` 开头。**

原因：

- `isXxx` 极易被滥用成“随便一个布尔值”的垃圾命名。
- 很多布尔含义本质上不是“是/否”，而是“是否拥有/允许/需要/应该”。
- `is` 前缀一旦习惯化，会让表达变得非常贫瘠。

推荐替代前缀：

- `has`：表示拥有某种属性或状态（`hasPermission`, `hasActiveSession`）
- `should`：表示是否应该执行某操作（`shouldRetry`, `shouldShowDialog`）
- `can`：表示能力或许可（`canAccessAdminPanel`, `canRetryPayment`）
- `needs`：表示需求（`needsRestart`, `needsMfaVerification`）
- 领域短语：`userHasActiveSession`, `paymentWasSuccessful`, `emailNeedsVerification`

示例（简化自原规范）：

```typescript
// ❌ 禁止
const isActive = true;
function isUserLoggedIn() {}
class IsVisibleChecker {}
interface IsEnabled {}

// ✅ 推荐
const userHasActiveSession = true;
const formHasValidInput = false;
const buttonShouldBeDisabled = true;

function userHasPermission(permission: string): boolean {
  return permissions.includes(permission);
}

class UserSessionValidator {}
interface ButtonVisibilityConfig {}
```

---

## 3. 语义化命名（变量、函数、类）

### 3.1 禁止的命名模式

以下写法全部禁止（仅极小局部循环索引 `i / j / k` 例外）：

- **单字母**：`a`, `x`, `f` 等
- **无意义通用词**：`data`, `info`, `item`, `value`, `temp`, `obj`, `arr`
- **含糊的 ID**：`id`, `ID`, `providerId`, `sourceId`（看名字不知道是哪一类 ID）
- **任何缩写**：`usr`, `cnt`, `idx`, `msg`, `cfg`, `btn` 等
- **以 `Data` 结尾的模糊命名**：`userData`, `listData`, `tableData`, `formData`
- **匈牙利命名法**：`strName`, `nCount`, `bIsActive`, `arrUsers` 等

示例：

```typescript
// ❌ 禁止
const data = {};
const id = '123';
const providerId = 'p-1';
const usr = {};
const cfg = {};
const userData = {};
const tableData = [];
```

### 3.2 推荐的命名模式

**变量 / 字段**：

- 使用能反映**实体 + 视角**的组合：`userProfile`, `orderDetails`, `normalizedUserList`
- 对集合使用复数/列表后缀：`userList`, `productList`, `rawUserList`
- 对计数使用计数后缀：`itemCount`, `retryCount`, `totalPrice`
- 对临时文件/路径写清语义：`temporaryFilePath`, `avatarUploadTempDir`

```typescript
// ✅ 推荐
const userProfile = {};
const orderDetails = {};
const productList = [];
const rawUserList = [];
const normalizedUserList = [];
const totalPrice = 100;
const temporaryFilePath = '/tmp/file';
const currentUser = {};
const userList = [];
const itemCount = 0;
const errorMessage = '';
const appConfig = {};
```

**ID 命名**：必须包含领域前缀，避免泛化的 `id`。

```typescript
// ✅ 推荐 - 带领域含义
const userAccountId = 'user-123';
const orderId = 'order-456';
const paymentProviderId = 'stripe';
const authProviderUserId = 'wx-openid-1';
const dataSourceId = 'import-job-001';
```

**布尔变量命名**：使用短语表达真实语义，避免机械的 `xxxFlag`。

```typescript
// ✅ 推荐
const userHasActiveSubscription = true;
const formContainsErrors = false;
const productIsInStock = true;
const paymentWasSuccessful = false;
const emailNeedsVerification = true;
```

**函数命名**：使用动词开头的动作短语。

```typescript
function calculateTotalPrice(items: CartItem[]): number {}
function validateEmailFormat(email: string): boolean {}
function fetchUserProfile(userId: string): Promise<User> {}
function transformDataToViewModel(data: ApiResponse): ViewModel {}
function persistOrderToDatabase(order: Order): Promise<void> {}
```

**类 / 接口命名**：用名词短语表达领域角色。

```typescript
class UserAccountManager {}
class PaymentTransactionProcessor {}
class EmailNotificationService {}
interface ShoppingCartRepository {}
interface OrderFulfillmentStrategy {}
```

---

## 4. 文件行数限制与重构

**单个源代码文件（`.ts` / `.tsx`）不得超过 400 行，超出必须重构为更小的模块。**

目的：

- 提升可读性与可导航性
- 降低合并冲突
- 聚焦单一职责，方便测试与复用

推荐拆分方式：

1. 按功能拆分：`user-crud.ts`, `user-auth.ts`, `user-password.ts`, `user-email.ts` 等
2. 提取公共逻辑到工具函数或独立模块
3. 使用组合替代复杂继承结构
4. 分离接口（types）和实现（services）

```text
// ❌ 禁止 - 超过 400 行的巨石文件
src/services/user-service.ts (450 行)
  - 用户 CRUD
  - 认证
  - 授权
  - 密码重置
  - 邮件发送

// ✅ 推荐 - 按功能拆分
src/services/user/
  ├── index.ts          # 统一导出
  ├── user-crud.ts      # 用户增删改查
  ├── user-auth.ts      # 用户认证
  ├── user-password.ts  # 密码管理
  └── user-email.ts     # 邮件服务
```

---

## 5. 空字符串禁止规则

**严禁将空字符串作为“没有值”的语义使用。**

- 默认值/初始值不要用空字符串
- 函数参数默认值不要是空字符串
- 配置对象中的空字符串必须有明确语义，否则改为 `null` / `undefined`

```typescript
// ❌ 禁止
const userName = '';
let description = '';
const defaultValue = '';

function greet(name: string = '') {
  return `Hello, ${name}`;
}

const config = {
  apiUrl: '',
  token: '',
};

// ✅ 推荐 - 使用 null / undefined 或有意义的默认值
const DEFAULT_USER_NAME = 'Guest';
const userName: string | null = null;
let description: string | undefined = undefined;

function greet(name: string = DEFAULT_USER_NAME) {
  return `Hello, ${name}`;
}

interface UserConfig {
  nickname?: string;
  bio?: string;
  avatarUrl: string | null;
}
```

在表单/状态管理中：

- 使用 `null` / `undefined` 表示“未填写/未加载”
- 只在真正需要空字符串语义（例如“用户刻意清空了字段”）时才存储 `''`

---

## 6. 解构与 `const` 使用

### 6.1 `const` 优先

- 对不会被重新赋值的引用（对象、数组、函数）一律使用 `const`
- 仅对真正会变化的值使用 `let`
- 禁止使用 `var`

```typescript
// ✅ 推荐
const PI = 3.14159;
const user = { name: 'John' };
let retryCount = 0;

// ❌ 避免
var name = 'John';
let PI = 3.14159;
```

### 6.2 解构赋值

- 使用解构从对象/数组中提取字段
- 适度使用重命名，保持变量在当前上下文的语义更明确

```typescript
// 对象解构
const { name, age } = user;
const { name: userName } = user;

// 数组解构
const [first, second] = items;
const [firstItem, ...restItems] = items;

// 函数参数解构
function processUser({ name, age }: User) {
  // ...
}
```

---

## 7. 类型命名与复用（与类型规范联动）

> 详细类型定义规则请参考：`languages/javascript-typescript/types.md`（如未来拆分）。

本节只强调与“命名”直接相关的约束：

- 所有类型定义（`interface` / `type` / `enum`）必须是**具名**的，且放在独立的 types 模块中管理。
- 不允许在函数签名中写内联对象类型（尤其是导出函数的 `options` 参数与返回值）。
- 避免重复定义“同字段同语义”的类型，优先复用已有类型。
- 命名时用后缀表达语义差异：`RawUser`, `NormalizedUser`, `PersistedUser`, `PublicUser`。

```typescript
// ❌ 禁止 - 内联类型与重复返回类型
export function normalizeDirectoryTreeOptions(options: {
  deep?: number;
  dot?: boolean;
}): {
  deep: number;
  dot: boolean;
} {
  // ...
}

// ✅ 推荐 - 抽成具名类型，并在 types 文件中维护
export interface NormalizeDirectoryTreeOptions {
  deep?: number;
  dot?: boolean;
}

export interface NormalizedDirectoryTreeOptions {
  deep: number;
  dot: boolean;
}

export function normalizeDirectoryTreeOptions(
  options: NormalizeDirectoryTreeOptions,
): NormalizedDirectoryTreeOptions {
  // ...
}
```

---

## 8. 使用方式

- **新增/修改 JS/TS 代码时，必须对照本文件进行命名自检。**
- 代码评审（Code Review）中，命名问题如果与本规范冲突，必须优先修正。
- 未来如果命名策略有调整，应**只修改本文件**，再同步到其他规范中的引用。

