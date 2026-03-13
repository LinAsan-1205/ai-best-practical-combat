# JavaScript / TypeScript 代码规范

## 0. 核心原则

### 0.1 规范优先原则
**所有代码必须严格遵循当前规范标准，无需考虑向后兼容性或遗留系统支持，仅针对目标环境实现最优解决方案。**

- 以当前规范为唯一标准，不妥协于历史代码风格
- 充分利用目标环境的最新特性，不迁就旧版本兼容性
- 优先实现最优方案，不因遗留系统而降低代码质量
- 新代码必须 100% 符合规范，不对旧代码做兼容适配

### 0.2 示例
```typescript
// ✅ 好的示例 - 使用最新特性，不考虑旧浏览器兼容
const result = data?.map(item => item.value).filter(Boolean) ?? [];

// ✅ 好的示例 - 使用目标环境原生 API，不引入 polyfill
const unique = [...new Set(items)];

// ❌ 避免 - 为了兼容旧代码而使用过时写法
var result = data && data.map ? data.map(function(item) { return item.value; }) : [];
```

## 1. 语言选择

- 新项目优先使用 **TypeScript**
- 现有 JavaScript 项目逐步迁移到 TypeScript
- 使用最新的 ECMAScript 特性（通过 Babel 或 TypeScript 编译）
- **禁止使用 `.js` 文件扩展名，必须使用 `.ts` 或 `.tsx`**（配置文件除外）

```typescript
// ❌ 禁止
utils.js
helpers.js
config.js

// ✅ 推荐
utils.ts
helpers.ts
config.ts
```

## 2. TypeScript 配置

### 2.1 推荐配置 (tsconfig.json)
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "node",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "resolveJsonModule": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## 3. 命名规范

### 3.0 硬编码规范

硬编码（Magic Values）需要**根据场景判断**是否提取为常量。以下场景**必须**使用常量，其他场景可直接使用字面量。

#### 必须使用常量的场景

| 场景 | 说明 | 示例 |
|------|------|------|
| **业务状态值** | 表示业务状态的字符串/数字 | 用户状态、订单状态、权限标识 |
| **配置参数** | 可调整的参数值 | 超时时间、重试次数、分页大小 |
| **外部接口** | 与外部系统交互的标识 | API 地址、错误码、响应消息 |
| **多处使用** | 在多个地方重复使用的值 | 默认值、边界值、魔法数字 |
| **可能变化** | 未来可能需要修改的值 | 版本号、限制值、阈值 |

```typescript
// ❌ 禁止 - 业务状态值硬编码
if (status === 'active') {}
if (order.status === 2) {}  // 2 代表什么？

// ❌ 禁止 - 配置参数硬编码
const timeout = 5000;  // 5秒？为什么是这个值？
const maxRetries = 3;  // 3次？可以调整吗？

// ❌ 禁止 - 错误消息硬编码
if (error.message === 'User not found') {}
throw new Error('Invalid credentials');

// ✅ 好的示例 - 使用常量
import { USER_STATUS_ACTIVE, ORDER_STATUS_PAID } from '@/constants/status';
import { REQUEST_TIMEOUT_MS, MAX_RETRY_ATTEMPTS } from '@/constants/config';
import { ERROR_USER_NOT_FOUND, ERROR_INVALID_CREDENTIALS } from '@/constants/error';

if (status === USER_STATUS_ACTIVE) {}
if (order.status === ORDER_STATUS_PAID) {}
const timeout = REQUEST_TIMEOUT_MS;  // 配置集中管理
const maxRetries = MAX_RETRY_ATTEMPTS;  // 含义清晰，便于调整
```

#### 可以直接使用字面量的场景

| 场景 | 说明 | 示例 |
|------|------|------|
| **语义明确** | 值本身就能说明含义 | `count > 0`, `index + 1` |
| **局部使用** | 只在当前上下文使用 | 循环计数器、临时计算 |
| **标准值** | 行业/语言标准值 | `Math.PI`, `100` (百分比) |
| **测试数据** | 测试用例中的数据 |  mock 数据、测试输入 |

```typescript
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
describe('calculator', () => {
  it('should add numbers', () => {
    expect(add(2, 3)).toBe(5);  // 测试数据直接使用
  });
});
```

#### 使用枚举（TypeScript）

对于一组相关的业务状态值，优先使用枚举：

```typescript
// ✅ 好的示例 - 使用枚举
enum UserStatus {
  ACTIVE = 'active',
  INACTIVE = 'inactive',
  PENDING = 'pending',
}

enum HttpStatusCode {
  OK = 200,
  CREATED = 201,
  BAD_REQUEST = 400,
  UNAUTHORIZED = 401,
  NOT_FOUND = 404,
  SERVER_ERROR = 500,
}

if (user.status === UserStatus.ACTIVE) {}
if (response.status === HttpStatusCode.OK) {}
```

#### 配置对象

对于相关的配置参数，使用配置对象组织：

```typescript
// ✅ 好的示例 - 配置对象
export const APP_CONFIG = {
  retry: {
    maxAttempts: 3,
    delayMs: 1000,
  },
  request: {
    timeout: 5000,
    headers: {
      'Content-Type': 'application/json',
    },
  },
  pagination: {
    defaultSize: 20,
    maxSize: 100,
  },
} as const;

// 使用
const timeout = APP_CONFIG.request.timeout;
```

#### 需要注释的场景

当使用字面量时，如果含义不够直观，应添加注释说明：

```typescript
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

#### 常量文件组织
```
constants/
├── index.ts              # 统一导出
├── app.ts                # 应用级常量
├── api.ts                # API 相关常量
├── user.ts               # 用户相关常量
├── error.ts              # 错误信息常量
├── config.ts             # 配置常量
└── ui.ts                 # UI 相关常量
```

#### 常量定义示例
```typescript
// constants/app.ts
export const APP_NAME = 'MyApplication';
export const APP_VERSION = '1.0.0';

export const MAX_RETRY_ATTEMPTS = 3;  // 网络请求失败后的最大重试次数
export const REQUEST_TIMEOUT_MS = 5000;  // 请求超时时间，单位毫秒
export const DEBOUNCE_DELAY_MS = 300;  // 输入防抖延迟，单位毫秒

export const DEFAULT_PAGE_SIZE = 20;  // 默认分页大小
export const MAX_PAGE_SIZE = 100;  // 最大分页大小

// constants/user.ts
export const USER_STATUS_ACTIVE = 'active';
export const USER_STATUS_INACTIVE = 'inactive';
export const USER_STATUS_PENDING = 'pending';

export const DEFAULT_USER_ROLE = 'user';
export const ADMIN_ROLE = 'admin';

export const USERNAME_MIN_LENGTH = 3;  // 用户名最小长度
export const USERNAME_MAX_LENGTH = 20;  // 用户名最大长度
export const PASSWORD_MIN_LENGTH = 8;  // 密码最小长度

// constants/error.ts
export const ERROR_USER_NOT_FOUND = 'User not found';
export const ERROR_INVALID_CREDENTIALS = 'Invalid credentials';
export const ERROR_UNAUTHORIZED = 'Unauthorized access';
export const ERROR_SERVER_ERROR = 'Internal server error';

// constants/api.ts
export const API_BASE_URL = process.env.VITE_API_URL || 'https://api.example.com';
export const API_VERSION = 'v1';

export const HTTP_STATUS_OK = 200;
export const HTTP_STATUS_CREATED = 201;
export const HTTP_STATUS_BAD_REQUEST = 400;
export const HTTP_STATUS_UNAUTHORIZED = 401;
export const HTTP_STATUS_NOT_FOUND = 404;
export const HTTP_STATUS_SERVER_ERROR = 500;

// constants/config.ts
export const APP_CONFIG = {
  retry: {
    maxAttempts: 3,  // 最大重试次数
    delayMs: 1000,   // 重试延迟，单位毫秒
  },
  request: {
    timeout: 5000,   // 请求超时时间
    headers: {
      'Content-Type': 'application/json',
    },
  },
  pagination: {
    defaultSize: 20,  // 默认每页条数
    maxSize: 100,     // 最大每页条数
  },
} as const;
```

### 3.1 禁止 "is" 前缀
**所有变量、函数、方法、类和接口名称禁止以 "is" 开头。**

使用以下替代方案：
- `has` - 表示拥有某种属性或状态
- `should` - 表示是否应该执行某操作
- `can` - 表示能力或许可
- `needs` - 表示需求
- `user` + 动作/状态 - 描述性布尔短语

```typescript
// ❌ 禁止 - 以 "is" 开头的命名
const isActive = true;
const isValid = false;
function isUserLoggedIn() {}
class IsVisibleChecker {}
interface IsEnabled {}

// ✅ 好的示例 - 使用替代方案
const userHasActiveSession = true;
const formHasValidInput = false;
const buttonShouldBeDisabled = true;
const userCanAccessResource = true;
const systemNeedsRestart = false;

// ✅ 好的示例 - 函数命名
function userHasPermission(permission: string): boolean {
  return permissions.includes(permission);
}

function formDataIsValid(data: FormData): boolean {
  return validateForm(data);
}

function buttonShouldShowLoading(state: LoadingState): boolean {
  return state.status === 'loading';
}

// ✅ 好的示例 - 类和接口
class UserSessionValidator {}
interface ButtonVisibilityConfig {}
class FormValidationChecker {}
```

### 3.2 语义化命名
**所有标识符必须使用具有明确语义的名字，清晰描述其目的和功能。**

#### 禁止的命名
```typescript
// ❌ 禁止 - 单字母（除标准循环索引外）
const a = 10;
const x = 'name';
function f() {}

// ❌ 禁止 - 无意义通用词
const data = {};
const info = {};
const item = {};
const value = 100;
const temp = 'temp';
const obj = {};
const arr = [];

// ❌ 禁止 - 含糊的 id 命名（看名字不知道是哪一类 ID）
const id = '123';           // 不知道是用户、订单还是其他 ID
const ID = '123';           // 同上，还混用大小写风格
const providerId = 'p-1';   // 是支付渠道？登录提供商？内部服务？名称不自解释
const sourceId = 's-1';     // 是数据来源？用户来源？导入来源？

// ❌ 禁止 - 任何缩写（即使有上下文也不允许）
const usr = {};  // user
const cnt = 0;   // count
const idx = 0;   // index
const msg = '';  // message
const cfg = {};  // config
const btn = {};  // button

// ❌ 禁止 - 以 Data 结尾的命名（含糊不清，没有语义边界）
const userData = {};        // 什么维度的数据？原始的？汇总的？
const listData = [];        // 具体是哪一类列表？
const formData = {};        // 与浏览器原生 FormData 概念冲突
const tableData = [];       // 结构、来源、用途都不清晰

// ❌ 禁止 - 匈牙利命名法
const strName = 'John';
const nCount = 10;
const bIsActive = true;
const arrUsers = [];
```

#### 推荐的命名
```typescript
// ✅ 好的示例 - 描述性命名
const userProfile = {};
const orderDetails = {};
const productList = [];
const rawUserList = [];
const normalizedUserList = [];
const totalPrice = 100;
const temporaryFilePath = '/tmp/file';
const userObject = {};
const userList = [];

const currentUser = {};
const itemCount = 0;
const userIndex = 0;
const errorMessage = '';
const appConfig = {};
const submitButton = {};

// ✅ 好的示例 - 带领域含义的 ID 命名（不依赖注释才能看懂）
const userAccountId = 'user-123';
const orderId = 'order-456';
const paymentProviderId = 'stripe';        // 支付渠道 ID
const authProviderUserId = 'wx-openid-1';  // 认证提供商里的用户 ID
const dataSourceId = 'import-job-001';     // 数据来源/导入任务 ID

// ✅ 好的示例 - 布尔值使用描述性复合名称
const userHasActiveSubscription = true;
const formContainsErrors = false;
const productIsInStock = true;
const paymentWasSuccessful = false;
const emailNeedsVerification = true;

// ✅ 好的示例 - 函数使用动作导向动词
function calculateTotalPrice(items: CartItem[]): number {}
function validateEmailFormat(email: string): boolean {}
function fetchUserProfile(userId: string): Promise<User> {}
function transformDataToViewModel(data: ApiResponse): ViewModel {}
function persistOrderToDatabase(order: Order): Promise<void> {}

// ✅ 好的示例 - 类和接口使用反映领域概念的名词短语
class UserAccountManager {}
class PaymentTransactionProcessor {}
class EmailNotificationService {}
interface ShoppingCartRepository {}
interface OrderFulfillmentStrategy {}
```

### 3.3 文件行数限制
**单个文件代码行数不得超过 400 行，超出必须重构为更小的模块。**

```typescript
// ❌ 禁止 - 超过 400 行的文件
// src/services/user-service.ts (450 行)
// 包含：用户 CRUD、认证、授权、密码重置、邮件发送...

// ✅ 好的示例 - 拆分为多个小模块
// src/services/user/
//   ├── index.ts          # 统一导出
//   ├── user-crud.ts      # 用户增删改查 (80 行)
//   ├── user-auth.ts      # 用户认证 (120 行)
//   ├── user-password.ts  # 密码管理 (100 行)
//   └── user-email.ts     # 邮件服务 (90 行)
```

#### 重构策略
1. **按功能拆分** - 将大文件按功能拆分为多个小文件
2. **提取公共逻辑** - 将重复代码提取到工具函数或基类
3. **使用组合替代继承** - 减少类层次结构复杂度
4. **分离接口和实现** - 接口定义与具体实现分离

### 3.4 使用 const 优先
```typescript
// 好的示例
const PI = 3.14159;
const user = { name: 'John' };
let count = 0;

// 避免
var name = 'John';
let PI = 3.14159; // 不会重新赋值的用 const
```

### 3.2 禁止空字符串
**严禁定义空字符串作为变量值或默认值。**

```typescript
// ❌ 禁止 - 定义空字符串
const userName = '';
let description = '';
const defaultValue = '';

// ❌ 禁止 - 函数参数默认值为空字符串
function greet(name: string = '') {
  return `Hello, ${name}`;
}

// ❌ 禁止 - 对象属性默认值为空字符串
const config = {
  apiUrl: '',
  token: ''
};

// ✅ 好的示例 - 使用 null 或 undefined
const userName: string | null = null;
let description: string | undefined = undefined;

// ✅ 好的示例 - 使用有意义的默认值
const DEFAULT_USER_NAME = 'Guest';
function greet(name: string = DEFAULT_USER_NAME) {
  return `Hello, ${name}`;
}

// ✅ 好的示例 - 类型定义中避免空字符串
interface UserConfig {
  // 使用可选属性而非空字符串
  nickname?: string;
  bio?: string;
  // 或明确允许 null
  avatarUrl: string | null;
}

// ✅ 好的示例 - 表单处理
// 使用 null 表示未填写，而非空字符串
const [inputValue, setInputValue] = useState<string | null>(null);

// 提交前验证
function submitForm() {
  if (inputValue === null || inputValue.trim() === '') {
    showError('请输入内容');
    return;
  }
  // 提交逻辑
}
```

### 3.3 类型定义规范
**所有类型必须在独立的 type 文件或接口中定义，禁止在代码中直接内联定义类型；禁止在类型中使用 `T | null` 这种“可为 null”的联合类型，没有值就用可选属性或 `undefined`。**

#### 3.3.1 类型与字段注释（强制）
**所有 TypeScript 类型定义（`interface` / `type` / `enum`）必须写文档注释，且类型的每个字段/成员也必须写注释（包括可选字段、嵌套对象字段）。**

- 类型本身：使用 `/** ... */` 说明该类型的职责/使用场景/关键约束
- 每个字段：使用 `/** ... */` 说明含义、单位/格式、取值范围、为空/缺省语义等
- 禁止用“看名字就知道”的理由省略字段注释（规范要求以**可读性与可维护性**为准）

```typescript
/** 用户资料（用于个人中心与评论展示） */
export interface UserProfile {
  /** 用户唯一标识（UUID） */
  userId: string;

  /** 展示名（允许为空字符串？如果不允许，请在校验层保证） */
  displayName: string;

  /** 头像 URL；未设置则为 undefined */
  avatarUrl?: string;

  /** 联系方式（不对外展示） */
  contact: {
    /** 邮箱地址（RFC 5322 格式的子集） */
    email: string;
    /** 手机号（E.164；未提供则为 undefined） */
    phone?: string;
  };
}

/** 订单状态（用于 UI 展示与筛选） */
export type OrderStatus =
  /** 待支付 */
  | 'pending'
  /** 已支付 */
  | 'paid'
  /** 已取消 */
  | 'cancelled';
```

#### 3.3.2 避免重复定义“同字段同语义”的类型/返回值（强制）
**如果某个对象的字段结构与语义与返回类型完全一致，就不要为了“显式”而：**

- 重复定义一个字段一模一样的新类型
- 重新组装一遍同字段对象再返回（`return { a: x.a, b: x.b }`）

应优先使用**类型复用**与**直接返回**；只有当你在边界处做了“补齐/转换/裁剪/重命名”等语义变化时，才定义新的 `Normalized*` / `Public*` / `Persisted*` 类型并显式映射。

```typescript
export type SearchOptions = {
  caseSensitive?: boolean;
  mode?: 'name' | 'path';
  root: string;
};

// ❌ 避免：同字段同语义还要重新组装
export function buildOptionsBad(options: SearchOptions): SearchOptions {
  return {
    caseSensitive: options.caseSensitive,
    mode: options.mode,
    root: options.root,
  };
}

// ✅ 推荐：字段/语义一致，直接返回
export function buildOptions(options: SearchOptions): SearchOptions {
  return options;
}

// ✅ 推荐：类型也不要重复定义，直接复用/别名
export type SearchOptionsInput = SearchOptions;
```

```typescript
// ❌ 禁止 - 直接在代码中定义类型
function processUser(user: { name: string; age: number }) {
  // ...
}

// ❌ 禁止 - 使用 typeof 进行类型检查
if (typeof value === 'string') {
  // ...
}

// ❌ 禁止 - 内联联合类型
const status: 'pending' | 'active' | 'inactive' = 'pending';

// ❌ 禁止 - 使用 `number | null` / `string | null` 等联合类型表示“可能没有值”
// 没有就是没有，直接用可选属性或 `undefined`，而不是强行并一个 null 进去
type Age = number | null;

interface UserWithNullableAge {
  age: number | null;
}

// ✅ 好的示例 - 使用可选属性或 `undefined`
type SafeAge = number | undefined;

interface User {
  age?: number;            // 可选属性，未提供即为“没有”
  nickname?: string;       // 字符串同理，不需要 `string | null`
}

function getUserAge(user: User): number | undefined {
  return user.age;
}

// ✅ 好的示例 - 在 types/user.ts 中定义
export interface User {
  name: string;
  age: number;
}

// ✅ 好的示例 - 在 types/status.ts 中定义
export type Status = 'pending' | 'active' | 'inactive';

// ✅ 好的示例 - 使用导入的类型
import { User } from '@/types/user';
import { Status } from '@/types/status';

function processUser(user: User) {
  // ...
}

const status: Status = 'pending';
```

### 3.4 解构赋值
```typescript
// 对象解构
const { name, age } = user;
const { name: userName } = user; // 重命名

// 数组解构
const [first, second] = items;
const [first, ...rest] = items;

// 函数参数解构
function processUser({ name, age }: User) {
  // ...
}
```

## 4. 代码组织

### 4.1 按功能模块组织代码

**核心原则：代码必须按功能模块组织，禁止零散放置（公共代码除外）**

#### ❌ 禁止的写法 - 零散放置
```
src/
├── utils.ts          # 零散的工具函数
├── helpers.ts        # 零散的帮助函数
├── format.ts         # 零散的业务逻辑
├── constants.ts      # 零散的所有常量
└── types.ts          # 零散的所有类型
```

#### ✅ 推荐的写法 - 按功能模块组织
```
src/
├── utils/            # 公共工具模块
│   ├── index.ts
│   ├── date.ts       # 日期相关
│   ├── string.ts     # 字符串相关
│   └── number.ts     # 数字相关
│
├── constants/        # 常量按功能分类
│   ├── index.ts
│   ├── app.ts        # 应用级常量
│   ├── user.ts       # 用户相关常量
│   └── api.ts        # API 相关常量
│
├── modules/          # 业务功能模块
│   ├── user/         # 用户模块
│   │   ├── index.ts
│   │   ├── types.ts
│   │   ├── constants.ts
│   │   ├── utils.ts
│   │   └── format-thinking.ts  # 模块内工具
│   │
│   └── order/        # 订单模块
│       ├── index.ts
│       ├── types.ts
│       └── utils.ts
│
└── shared/           # 真正的公共代码
    ├── index.ts
    └── common.ts
```

#### 规范要点

1. **功能内聚**：相关代码放在同一模块目录下
2. **避免大文件**：单个文件代码行数不得超过 400 行，超出必须重构为更小的模块
3. **公共代码例外**：真正通用的代码放在 `shared/` 或 `utils/` 目录
4. **就近原则**：工具函数优先放在使用它的模块内，只有被多个模块使用时才放到公共目录

### 4.3 对象字面量中的函数调用（强制）
**在对象字面量中禁止直接写复杂表达式/调用函数（哪怕只有一次简单调用），必须提前提取为具名变量后再使用。**

```typescript
// ❌ 禁止：在对象字面量里直接调用函数、做复杂运算
const item = {
  highlights: createSearchHighlights(nameRanges, pathRanges),
  name: document.name,
  path: document.path,
  score: getSearchItemScore(nameRanges, pathRanges),
  type: document.type,
};

// ❌ 禁止：即使只有单个字段调用函数也不允许
const result = {
  nameRanges: getMatchRanges(document.name, query, caseSensitive),
  pathRanges: [],
};

// ✅ 推荐：上方定义清晰的中间变量，对象构造保持“纯数据”
const highlights = createSearchHighlights(nameRanges, pathRanges);
const score = getSearchItemScore(nameRanges, pathRanges);
const nameRanges = getMatchRanges(document.name, query, caseSensitive);

const itemOk = {
  highlights,
  name: document.name,
  path: document.path,
  score,
  type: document.type,
};

const resultOk = {
  nameRanges,
  pathRanges: [],
};
```

## 5. 类型定义

### 4.1 接口 vs 类型别名
```typescript
// 对象结构优先使用 interface
interface User {
  id: string;
  name: string;
  email: string;
}

// 联合类型、元组等使用 type
type Status = 'pending' | 'active' | 'inactive';
type Point = [number, number];

// 函数类型使用 type
type Handler = (event: Event) => void;
```

### 4.2 泛型使用
```typescript
// 好的示例
function identity<T>(arg: T): T {
  return arg;
}

interface Repository<T> {
  findById(id: string): Promise<T | null>;
  save(entity: T): Promise<T>;
}

// 约束泛型
function process<T extends { id: string }>(item: T): void {
  console.log(item.id);
}
```

### 4.3 避免使用 any
```typescript
// 避免
function process(data: any): any {
  return data.value;
}

// 好的示例 - 使用 unknown
function process(data: unknown): string {
  if (typeof data === 'string') {
    return data;
  }
  throw new Error('Invalid data');
}

// 或使用具体类型
function process(data: { value: string }): string {
  return data.value;
}
```

## 6. 函数

### 5.0 函数注释（强制）
**每个函数都必须写文档注释（包括普通函数、类方法、导出函数、回调/处理器等）。**

- 使用 `/** ... */` 描述该函数的职责、关键约束与副作用（如 I/O、缓存、事务、状态变更）
- 对外可复用/被导出的函数：必须写清楚参数语义、返回值语义、错误/异常与边界条件
- 纯内部且实现非常直观的函数：仍需有最小注释（至少一句话说明意图），避免“看代码才能懂”

```typescript
/**
 * 计算购物车总价（不含运费）。
 * - 金额单位：分（integer）
 * - 规则：仅统计勾选商品；单价 * 数量后求和
 */
export function calculateCartTotal(items: CartItem[]): number {
  return items
    .filter(i => i.selected)
    .reduce((sum, i) => sum + i.unitPriceCents * i.quantity, 0);
}

/**
 * 从后端拉取用户资料。
 * @throws 当响应非 2xx 或解析失败时抛出 Error
 */
export async function fetchUserProfile(userId: string): Promise<UserProfile> {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) {
    throw new Error(`Failed to fetch user profile: ${response.statusText}`);
  }
  return response.json() as Promise<UserProfile>;
}
```

### 5.1 箭头函数
```typescript
// 简单函数使用箭头函数
const add = (a: number, b: number): number => a + b;

// 多行函数
const calculate = (a: number, b: number): number => {
  const sum = a + b;
  return sum * 2;
};

// 回调函数
items.map(item => item.name);
items.filter(item => item.active);
```

### 5.2 默认参数
```typescript
function greet(name: string, greeting: string = 'Hello'): string {
  return `${greeting}, ${name}!`;
}

// 避免在参数中使用解构默认值
function process({ timeout = 5000 }: Options = {}): void {
  // ...
}
```

### 5.3 异步函数
```typescript
// 优先使用 async/await
async function fetchUser(id: string): Promise<User> {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) {
    throw new Error(`Failed to fetch user: ${response.statusText}`);
  }
  return response.json();
}

// Promise 链式调用保持简洁
fetchUser(id)
  .then(user => processUser(user))
  .catch(error => handleError(error));
```

## 7. 类

### 6.1 类定义
```typescript
class UserService {
  // 私有属性使用 # 或 private
  #cache: Map<string, User> = new Map();
  private apiClient: ApiClient;

  constructor(apiClient: ApiClient) {
    this.apiClient = apiClient;
  }

  // 公共方法
  async getUser(id: string): Promise<User | null> {
    if (this.#cache.has(id)) {
      return this.#cache.get(id)!;
    }
    const user = await this.fetchFromApi(id);
    this.#cache.set(id, user);
    return user;
  }

  // 私有方法
  private async fetchFromApi(id: string): Promise<User> {
    return this.apiClient.get(`/users/${id}`);
  }
}
```

### 6.2 访问修饰符
- 显式声明访问修饰符（`public`, `private`, `protected`）
- 私有属性优先使用 `#` 私有字段语法
- 只读属性使用 `readonly`

## 8. 模块

### 7.1 导入导出
```typescript
// 命名导出
export class UserService {}
export interface User {}

// 默认导出（一个模块只有一个）
export default class MainComponent {}

// 导入
import { UserService, User } from './user';
import MainComponent from './main-component';
import * as utils from './utils';

// 类型导入
import type { User } from './types';
```

### 7.2 路径别名
```typescript
// 使用路径别名避免深层相对路径
import { UserService } from '@/services/user';
import { Button } from '@/components/ui';

// 避免
import { UserService } from '../../../../services/user';
```

### 7.3 导入顺序规范（参考 Airbnb）

**导入必须按以下顺序分组，每组之间保留一个空行：**

```typescript
// 1. 内置模块（Node.js 内置）
import fs from 'fs';
import path from 'path';

// 2. 外部依赖（npm 包）
import React from 'react';
import { useState, useEffect } from 'react';
import axios from 'axios';
import { isEmpty, debounce } from 'lodash-es';

// 3. 内部绝对路径导入（使用路径别名）
import { UserService } from '@/services/user';
import { Button } from '@/components/ui';
import { API_BASE_URL } from '@/constants/config';

// 4. 内部相对路径导入
import { helper } from '../utils/helper';
import { localConfig } from './config';

// 5. 类型导入（放在最后）
import type { User, Order } from '@/types';
import type { ComponentProps } from './types';
```

#### 禁止的导入写法
```typescript
// ❌ 禁止 - 混合导入顺序
import { Button } from '@/components/ui';
import React from 'react';
import { helper } from '../utils/helper';
import axios from 'axios';

// ❌ 禁止 - 相对路径超过两级
import { service } from '../../../services/api';

// ❌ 禁止 - 从目录导入（没有 index.ts 导出）
import { utils } from '@/utils';

// ❌ 禁止 - 循环依赖
// a.ts 导入 b.ts，b.ts 又导入 a.ts
```

#### 导入命名规范
```typescript
// ✅ 好的示例 - 具名导入使用原始名称
import { useState, useEffect } from 'react';

// ✅ 好的示例 - 必要时重命名（避免命名冲突）
import { Button as AntButton } from 'antd';
import { Button as CustomButton } from '@/components/ui';

// ❌ 避免 - 无意义重命名
import { useState as useReactState } from 'react';
```

### 7.4 模块组织原则

#### 单一职责
```typescript
// ❌ 避免 - 一个模块导出过多无关内容
// utils.ts
export function formatDate() {}
export function validateEmail() {}
export function calculateTax() {}
export function generateId() {}

// ✅ 好的示例 - 按功能拆分
// utils/date.ts
export function formatDate() {}

// utils/validation.ts
export function validateEmail() {}

// utils/finance.ts
export function calculateTax() {}

// utils/id.ts
export function generateId() {}
```

#### 重新导出模式
```typescript
// ✅ 好的示例 - 使用 index.ts 统一导出
// services/index.ts
export { UserService } from './user-service';
export { OrderService } from './order-service';
export { ProductService } from './product-service';

// 使用方
import { UserService, OrderService } from '@/services';
```

#### 目录导入规范
```typescript
// ❌ 避免 - 显式指定 index 文件路径
export * from './utils/index';
export { helper } from './components/index';

// ✅ 好的示例 - 省略 index，直接导入目录
export * from './utils';
export { helper } from './components';

// ✅ 好的示例 - 使用方也从目录导入
import { utils } from './utils';
import { components } from './components';
```

**说明**：当目录中存在 `index.ts` 或 `index.js` 文件时，模块系统会自动解析该文件。显式添加 `/index` 是冗余的，应该省略以简化导入路径。

## 9. React 性能优化规范

基于 Vercel React Best Practices 的性能优化指南。

### 8.1 异步优化（Eliminating Waterfalls）

#### 并行请求
```typescript
// ❌ 避免 - 串行请求（瀑布流）
async function loadDashboard() {
  const user = await fetchUser();      // 等待
  const orders = await fetchOrders();  // 等待
  const products = await fetchProducts(); // 等待
}

// ✅ 好的示例 - 并行请求
async function loadDashboard() {
  const [user, orders, products] = await Promise.all([
    fetchUser(),
    fetchOrders(),
    fetchProducts()
  ]);
}

// ✅ 好的示例 - 使用 better-all 处理部分依赖
import { all } from 'better-all';

async function loadDashboard() {
  const { user, orders, products } = await all({
    user: fetchUser(),
    orders: fetchOrders(),
    products: fetchProducts()
  });
}
```

#### 延迟 await
```typescript
// ❌ 避免 - 过早 await
async function getData() {
  const data = await fetchData();
  if (!shouldFetch) {
    return null;
  }
  return processData(data);
}

// ✅ 好的示例 - 将 await 移到实际使用处
async function getData() {
  if (!shouldFetch) {
    return null;
  }
  const data = await fetchData();
  return processData(data);
}
```

#### API 路由优化
```typescript
// ✅ 好的示例 - 尽早启动 Promise，延迟 await
export async function GET() {
  const userPromise = fetchUser();
  const ordersPromise = fetchOrders();
  
  const user = await userPromise;
  const orders = await ordersPromise;
  
  return Response.json({ user, orders });
}
```

### 8.2 Bundle 优化

#### 动态导入
```typescript
// ✅ 好的示例 - 使用 next/dynamic 延迟加载重型组件
import dynamic from 'next/dynamic';

const HeavyChart = dynamic(() => import('./HeavyChart'), {
  loading: () => <p>Loading chart...</p>,
  ssr: false // 如果组件不需要 SSR
});

// ✅ 好的示例 - 条件加载
const Analytics = dynamic(() => import('./Analytics'), {
  loading: () => null
});

function Page({ showAnalytics }) {
  return (
    <div>
      {showAnalytics && <Analytics />}
    </div>
  );
}
```

#### 直接导入
```typescript
// ❌ 避免 - 使用 barrel 文件导入
import { Button, Input, Modal } from '@/components';

// ✅ 好的示例 - 直接导入具体模块
import { Button } from '@/components/Button';
import { Input } from '@/components/Input';
import { Modal } from '@/components/Modal';
```

#### 预加载
```typescript
// ✅ 好的示例 - 悬停/聚焦时预加载
function Link({ href, children }) {
  return (
    <a
      href={href}
      onMouseEnter={() => {
        // 预加载目标页面
        const link = document.createElement('link');
        link.rel = 'prefetch';
        link.href = href;
        document.head.appendChild(link);
      }}
    >
      {children}
    </a>
  );
}
```

### 8.3 服务端性能优化

#### 缓存策略
```typescript
// ✅ 好的示例 - 使用 LRU 缓存跨请求数据
import { LRUCache } from 'lru-cache';

const cache = new LRUCache({
  max: 500,
  ttl: 1000 * 60 * 5 // 5分钟
});

export async function getUser(id: string) {
  if (cache.has(id)) {
    return cache.get(id);
  }
  const user = await fetchUserFromDB(id);
  cache.set(id, user);
  return user;
}
```

#### 静态资源提升
```typescript
// ✅ 好的示例 - 将静态 I/O 提升到模块级别
import fs from 'fs';
import path from 'path';

// 在模块级别读取，避免每次请求都读取
const logoSvg = fs.readFileSync(
  path.join(process.cwd(), 'public', 'logo.svg'),
  'utf-8'
);

export function Logo() {
  return <div dangerouslySetInnerHTML={{ __html: logoSvg }} />;
}
```

#### 序列化优化
```typescript
// ❌ 避免 - 传递大量数据到客户端组件
'use client';
function ClientComponent({ hugeData }) {
  // hugeData 会被序列化到客户端
}

// ✅ 好的示例 - 最小化传递给客户端的数据
'use server';
async function ServerComponent() {
  const data = await fetchData();
  const minimalData = data.map(item => ({
    id: item.id,
    name: item.name
    // 只传递必要字段
  }));
  
  return <ClientComponent data={minimalData} />;
}
```

### 8.4 客户端数据获取

#### SWR 自动去重
```typescript
// ✅ 好的示例 - 使用 SWR 自动去重请求
import useSWR from 'swr';

function UserProfile({ userId }) {
  // 多个组件使用相同 key 只会发起一次请求
  const { data: user } = useSWR(`/api/users/${userId}`, fetcher);
  const { data: posts } = useSWR(
    user ? `/api/users/${userId}/posts` : null,
    fetcher
  );
  
  return (
    <div>
      <h1>{user?.name}</h1>
      <PostList posts={posts} />
    </div>
  );
}
```

#### 事件监听器去重
```typescript
// ❌ 避免 - 重复添加全局事件监听器
useEffect(() => {
  window.addEventListener('scroll', handleScroll);
  return () => window.removeEventListener('scroll', handleScroll);
}, []);

// ✅ 好的示例 - 使用被动监听器优化滚动
useEffect(() => {
  window.addEventListener('scroll', handleScroll, { passive: true });
  return () => window.removeEventListener('scroll', handleScroll);
}, []);
```

### 8.5 重渲染优化

#### 使用原始值作为依赖
```typescript
// ❌ 避免 - 使用对象作为依赖
useEffect(() => {
  fetchData(filters);
}, [filters]); // filters 是对象，每次渲染都是新引用

// ✅ 好的示例 - 使用原始值
useEffect(() => {
  fetchData({ category, status });
}, [category, status]); // 原始值，稳定比较
```

#### 派生状态
```typescript
// ❌ 避免 - 在 effect 中派生状态
const [fullName, setFullName] = useState('');
useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// ✅ 好的示例 - 在渲染时派生
const fullName = `${firstName} ${lastName}`;

// ✅ 好的示例 - 使用 useMemo 缓存复杂计算
const sortedUsers = useMemo(() => {
  return users.sort((a, b) => b.score - a.score);
}, [users]);
```

#### 函数式 setState
```typescript
// ❌ 避免 - 依赖外部状态
const increment = () => setCount(count + 1);

// ✅ 好的示例 - 使用函数式更新
const increment = () => setCount(c => c + 1);

// ✅ 好的示例 - 复杂状态更新
const addItem = (item) => setItems(prev => [...prev, item]);
```

#### 延迟状态初始化
```typescript
// ❌ 避免 - 每次渲染都执行昂贵计算
const [data, setData] = useState(expensiveComputation());

// ✅ 好的示例 - 使用函数延迟初始化
const [data, setData] = useState(() => expensiveComputation());
```

#### 使用 ref 存储临时值
```typescript
// ❌ 避免 - 使用 state 存储频繁变化的值
const [mousePosition, setMousePosition] = useState({ x: 0, y: 0 });
useEffect(() => {
  const handler = (e) => setMousePosition({ x: e.clientX, y: e.clientY });
  window.addEventListener('mousemove', handler);
  return () => window.removeEventListener('mousemove', handler);
}, []);

// ✅ 好的示例 - 使用 ref 存储临时值
const mousePosition = useRef({ x: 0, y: 0 });
useEffect(() => {
  const handler = (e) => {
    mousePosition.current = { x: e.clientX, y: e.clientY };
  };
  window.addEventListener('mousemove', handler);
  return () => window.removeEventListener('mousemove', handler);
}, []);
```

#### 使用 startTransition
```typescript
// ✅ 好的示例 - 使用 startTransition 处理非紧急更新
import { startTransition } from 'react';

function handleChange(value) {
  // 紧急更新：输入框显示
  setInputValue(value);
  
  // 非紧急更新：搜索建议
  startTransition(() => {
    setSearchResults(search(value));
  });
}
```

### 8.6 渲染性能优化

#### 静态 JSX 提升
```typescript
// ❌ 避免 - 在组件内创建静态 JSX
function Component() {
  const header = <header><h1>Title</h1></header>;
  return <div>{header}</div>;
}

// ✅ 好的示例 - 将静态 JSX 提升到组件外
const Header = <header><h1>Title</h1></header>;

function Component() {
  return <div>{Header}</div>;
}
```

#### 长列表优化
```typescript
// ✅ 好的示例 - 使用 content-visibility 优化长列表
function LongList({ items }) {
  return (
    <div style={{ contentVisibility: 'auto' }}>
      {items.map(item => (
        <ListItem key={item.id} item={item} />
      ))}
    </div>
  );
}

// ✅ 好的示例 - 使用虚拟滚动
import { VirtualList } from 'react-virtualized';

function VirtualizedList({ items }) {
  return (
    <VirtualList
      width={300}
      height={500}
      rowCount={items.length}
      rowHeight={50}
      rowRenderer={({ index, style }) => (
        <div key={items[index].id} style={style}>
          {items[index].name}
        </div>
      )}
    />
  );
}
```

#### 使用 useTransition 处理加载状态
```typescript
// ✅ 好的示例 - 使用 useTransition 避免加载状态闪烁
import { useTransition } from 'react';

function SearchResults({ query }) {
  const [isPending, startTransition] = useTransition();
  const [results, setResults] = useState([]);
  
  useEffect(() => {
    startTransition(() => {
      fetchResults(query).then(setResults);
    });
  }, [query]);
  
  return (
    <div>
      {isPending && <Spinner />}
      <ResultsList results={results} />
    </div>
  );
}
```

### 8.7 JavaScript 性能优化

#### 批量 DOM 操作
```typescript
// ❌ 避免 - 多次修改样式
element.style.width = '100px';
element.style.height = '100px';
element.style.backgroundColor = 'red';

// ✅ 好的示例 - 使用 cssText 批量修改
element.style.cssText = 'width: 100px; height: 100px; background-color: red;';

// ✅ 好的示例 - 使用 className
element.className = 'box-large red';
```

#### 使用 Map 优化查找
```typescript
// ❌ 避免 - 数组重复查找
const user = users.find(u => u.id === id);

// ✅ 好的示例 - 构建 Map 优化查找
const userMap = new Map(users.map(u => [u.id, u]));
const user = userMap.get(id); // O(1) 查找
```

#### 缓存属性访问
```typescript
// ❌ 避免 - 循环中重复访问属性
for (let i = 0; i < items.length; i++) {
  process(items[i]);
}

// ✅ 好的示例 - 缓存长度
for (let i = 0, len = items.length; i < len; i++) {
  process(items[i]);
}

// ✅ 更好的示例 - 使用 for-of
for (const item of items) {
  process(item);
}
```

#### 合并迭代
```typescript
// ❌ 避免 - 多次遍历
const filtered = items.filter(i => i.active);
const mapped = filtered.map(i => i.name);
const result = mapped.slice(0, 10);

// ✅ 好的示例 - 合并为单次循环
const result = [];
for (const item of items) {
  if (item.active) {
    result.push(item.name);
    if (result.length >= 10) break;
  }
}
```

#### 提前返回
```typescript
// ❌ 避免 - 深层嵌套
function process(data) {
  if (data) {
    if (data.items) {
      if (data.items.length > 0) {
        return data.items[0];
      }
    }
  }
  return null;
}

// ✅ 好的示例 - 提前返回
function process(data) {
  if (!data) return null;
  if (!data.items) return null;
  if (data.items.length === 0) return null;
  return data.items[0];
}
```

### 8.8 高级模式

#### 事件处理器缓存
```typescript
// ❌ 避免 - 每次渲染创建新函数
function Component() {
  const handleClick = () => { /* ... */ };
  return <button onClick={handleClick}>Click</button>;
}

// ✅ 好的示例 - 使用 useCallback 缓存
function Component() {
  const handleClick = useCallback(() => { /* ... */ }, []);
  return <button onClick={handleClick}>Click</button>;
}

// ✅ 更好的示例 - 将事件处理器存储在 ref 中
function Component() {
  const handlerRef = useRef(() => { /* ... */ });
  return <button onClick={handlerRef.current}>Click</button>;
}
```

#### 单次初始化
```typescript
// ✅ 好的示例 - 应用级别单次初始化
let initialized = false;

function initializeApp() {
  if (initialized) return;
  initialized = true;
  
  // 初始化逻辑
  setupAnalytics();
  loadConfig();
}

function App() {
  useEffect(() => {
    initializeApp();
  }, []);
  
  return <div>App</div>;
}
```

### 8.9 组件定义
```typescript
// 函数组件
interface Props {
  title: string;
  onClick?: () => void;
}

export const Button: React.FC<Props> = ({ title, onClick }) => {
  return <button onClick={onClick}>{title}</button>;
};

// 或使用更简洁的方式
export function Button({ title, onClick }: Props) {
  return <button onClick={onClick}>{title}</button>;
}
```

### 8.2 Hooks 使用

#### Hooks 基本规则（参考 Airbnb React Hooks 规范）
```typescript
// ✅ 好的示例 - Hooks 放在组件顶部
function UserProfile({ userId }: { userId: string }) {
  const [user, setUser] = useState<User | null>(null);
  const [loading, setLoading] = useState(false);
  
  useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(setUser)
      .finally(() => setLoading(false));
  }, [userId]);

  // ...
}
```

#### Hooks 调用规则
```typescript
// ❌ 禁止 - 在条件语句中使用 Hooks
function Component({ shouldFetch }) {
  if (shouldFetch) {
    const [data, setData] = useState(null); // 错误！
  }
}

// ❌ 禁止 - 在循环中使用 Hooks
function Component({ items }) {
  for (let i = 0; i < items.length; i++) {
    const [value, setValue] = useState(items[i]); // 错误！
  }
}

// ❌ 禁止 - 在嵌套函数中使用 Hooks
function Component() {
  function handleClick() {
    const [count, setCount] = useState(0); // 错误！
  }
}

// ✅ 好的示例 - 始终在组件顶层调用 Hooks
function Component({ shouldFetch, items }) {
  const [data, setData] = useState(null);
  const [values, setValues] = useState(items);
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    if (shouldFetch) {
      fetchData().then(setData);
    }
  }, [shouldFetch]);
}
```

#### useEffect 依赖管理
```typescript
// ❌ 避免 - 遗漏依赖项
function Component({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, []); // 遗漏了 userId 依赖
}

// ✅ 好的示例 - 完整依赖数组
function Component({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetchUser(userId).then(setUser);
  }, [userId]); // 包含所有依赖
}

// ✅ 好的示例 - 使用函数式更新避免依赖
function Counter() {
  const [count, setCount] = useState(0);
  
  useEffect(() => {
    const timer = setInterval(() => {
      setCount(c => c + 1); // 函数式更新，不需要 count 依赖
    }, 1000);
    return () => clearInterval(timer);
  }, []); // 空依赖数组是安全的
}
```

#### 自定义 Hooks 规范
```typescript
// ✅ 好的示例 - 自定义 Hooks 以 use 开头
function useLocalStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      return initialValue;
    }
  });

  const setValue = useCallback((value: T | ((val: T) => T)) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  }, [key, storedValue]);

  return [storedValue, setValue] as const;
}

// ✅ 好的示例 - 自定义 Hooks 返回数组或对象
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        const json = await response.json();
        setData(json);
      } catch (err) {
        setError(err instanceof Error ? err : new Error('Unknown error'));
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// 使用
const { data: user, loading, error } = useFetch<User>('/api/user');
```

#### useCallback 和 useMemo 使用原则
```typescript
// ❌ 避免 - 过度使用 useCallback
function Component() {
  const handleClick = useCallback(() => {
    console.log('clicked');
  }, []); // 简单函数不需要缓存

  return <button onClick={handleClick}>Click</button>;
}

// ✅ 好的示例 - 在需要时使用 useCallback
function ParentComponent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState('');

  // 传递给子组件的回调需要缓存
  const handleSubmit = useCallback((data: FormData) => {
    submitForm(data);
  }, []);

  // 依赖外部状态的回调
  const handleIncrement = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  return (
    <div>
      <ChildComponent onSubmit={handleSubmit} />
      <button onClick={handleIncrement}>{count}</button>
    </div>
  );
}

// ✅ 好的示例 - 在需要时使用 useMemo
function DataTable({ data, sortKey }) {
  // 昂贵的排序操作需要缓存
  const sortedData = useMemo(() => {
    return [...data].sort((a, b) => a[sortKey].localeCompare(b[sortKey]));
  }, [data, sortKey]);

  // 对象/数组常量需要缓存
  const config = useMemo(() => ({
    pagination: { pageSize: 20 },
    sorting: { enabled: true }
  }), []);

  return <Table data={sortedData} config={config} />;
}
```

## 10. 工具函数规范

### 9.1 使用 lodash-es
**优先使用 lodash-es 提供的工具函数，避免手写工具函数。**

```typescript
// ❌ 禁止 - 手写工具函数
function isEmptyString(value: unknown): boolean {
  return typeof value === 'string' && value.length === 0;
}

function isNil(value: unknown): boolean {
  return value === null || value === undefined;
}

function debounce<T extends (...args: any[]) => any>(
  fn: T,
  delay: number
): (...args: Parameters<T>) => void {
  let timer: ReturnType<typeof setTimeout>;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

// ✅ 好的示例 - 使用 lodash-es
import { isEmpty, isNil, debounce, throttle, cloneDeep, pick, omit } from 'lodash-es';

// 检查空值
if (isNil(value)) { }
if (isEmpty(array)) { }

// 防抖节流
const debouncedSearch = debounce(handleSearch, 300);
const throttledScroll = throttle(handleScroll, 100);

// 对象操作
const cloned = cloneDeep(original);
const picked = pick(user, ['name', 'email']);
const omitted = omit(user, ['password']);
```

### 9.2 常用 lodash-es 函数
```typescript
import {
  // 类型检查
  isString, isNumber, isBoolean, isArray, isObject, isFunction,
  isNil, isNull, isUndefined, isEmpty,
  
  // 集合操作
  map, filter, reduce, find, some, every, includes,
  
  // 对象操作
  get, set, has, pick, omit, cloneDeep, merge,
  
  // 函数操作
  debounce, throttle, memoize, curry,
  
  // 字符串操作
  camelCase, kebabCase, snakeCase, startCase, trim,
  
  // 数组操作
  chunk, flatten, uniq, sortBy, groupBy, keyBy
} from 'lodash-es';
```

### 9.3 使用 destr 替代 JSON.parse
**优先使用 destr 替代原生 JSON.parse，特别是在处理不可信输入时。**

[destr](https://github.com/unjs/destr) 是一个更快、更安全、更方便的 JSON.parse 替代方案，由 UnJS 团队维护。

#### 安装
```bash
npm i destr
# 或
yarn add destr
# 或
pnpm i destr
```

#### 为什么使用 destr

1. **类型安全** - 默认返回 `unknown` 类型，支持泛型
2. **快速回退** - 非字符串输入直接返回原值，不会抛出错误
3. **已知字符串值快速查找** - 自动处理 `"TRUE"`、`"true"` 等布尔值
4. **解析失败回退** - 解析失败时返回原始值（而非抛出异常）
5. **避免原型污染** - 自动清理 `__proto__` 等危险属性

#### 使用示例

```typescript
import { destr, safeDestr } from 'destr';

// ✅ 好的示例 - 基础用法
const obj = destr('{ "name": "John" }'); // { name: 'John' }

// ✅ 好的示例 - 带类型
interface User {
  name: string;
  age: number;
}
const user = destr<User>('{ "name": "John", "age": 30 }');

// ✅ 好的示例 - 非字符串输入不会报错
const result1 = destr(undefined); // undefined
const result2 = destr(null);      // null
const result3 = destr(123);       // 123

// ✅ 好的示例 - 自动处理布尔值
const bool1 = destr('TRUE');   // true
const bool2 = destr('true');   // true
const bool3 = destr('FALSE');  // false

// ✅ 好的示例 - 解析失败返回原值
const fallback = destr('invalid json'); // 'invalid json'

// ✅ 好的示例 - 严格模式（解析失败抛出错误）
try {
  const strict = safeDestr<User>('{ invalid }');
} catch (error) {
  // 处理解析错误
}
```

#### 与 JSON.parse 对比

```typescript
// ❌ 避免 - JSON.parse 的问题
// 1. 类型不安全
const obj1 = JSON.parse('{}'); // obj1 类型是 any

// 2. 非字符串输入会报错
JSON.parse(undefined); // SyntaxError

// 3. 布尔值字符串解析失败
JSON.parse('TRUE'); // SyntaxError

// 4. 解析失败抛出异常
try {
  JSON.parse('invalid');
} catch (e) {
  // 必须捕获
}

// 5. 存在原型污染风险
const malicious = '{"__proto__": {"isAdmin": true}}';
JSON.parse(malicious); // 危险！

// ✅ 好的示例 - destr 解决以上问题
import { destr } from 'destr';

// 1. 类型安全（默认 unknown，支持泛型）
const obj2 = destr('{}'); // obj2 类型是 unknown
const obj3 = destr<User>('{}'); // obj3 类型是 User

// 2. 非字符串直接返回
const result = destr(undefined); // undefined，不报错

// 3. 自动处理布尔值
const bool = destr('TRUE'); // true

// 4. 解析失败返回原值
const fallback = destr('invalid'); // 'invalid'，不抛出

// 5. 自动防止原型污染
const safe = destr(malicious); // { user: {} }，已清理
```

#### 适用场景

- **API 响应解析** - 处理可能非 JSON 的响应
- **配置读取** - 解析环境变量或配置文件
- **用户输入** - 处理不可信的用户输入
- **数据存储** - 从 localStorage 等存储中读取数据
- **日志处理** - 解析可能损坏的 JSON 日志

```typescript
// ✅ 好的示例 - API 响应处理
async function fetchUser(id: string): Promise<User | null> {
  const response = await fetch(`/api/users/${id}`);
  const text = await response.text();
  // 即使返回非 JSON（如 HTML 错误页面），也不会崩溃
  return destr<User>(text);
}

// ✅ 好的示例 - localStorage 读取
function getStoredData<T>(key: string): T | null {
  const stored = localStorage.getItem(key);
  return destr<T>(stored);
}

// ✅ 好的示例 - 环境变量解析
const config = destr<Config>(process.env.APP_CONFIG);
```

## 11. 条件表达式规范

### 10.1 减少三元运算符使用
**非必要不使用三元运算符，优先使用 if/else 或提前返回。**

#### 10.1.1 禁止“分支赋值型三元”（强制）
**禁止用三元运算符在多处分支给变量赋值/构造值（尤其是多行三元、或同一意图写两段三元）。这种逻辑必须抽离为函数，或改为显式 `if/else`。**

典型禁止写法（示例）：

```typescript
// ❌ 禁止：同一意图写两段三元来构造结果
const nameRanges = searchMode === 'path'
  ? []
  : getMatchRanges(document.name, trimmedQuery, caseSensitive);

const pathRanges = searchMode === 'name'
  ? []
  : getMatchRanges(document.path, trimmedQuery, caseSensitive);
```

推荐写法（抽离函数，意图更集中）：

```typescript
type SearchMode = 'name' | 'path';

function getDocumentMatchRanges(args: {
  document: { name: string; path: string };
  query: string;
  caseSensitive: boolean;
  searchMode: SearchMode;
}): { nameRanges: number[]; pathRanges: number[] } {
  const { document, query, caseSensitive, searchMode } = args;

  if (searchMode === 'path') {
    return {
      nameRanges: [],
      pathRanges: getMatchRanges(document.path, query, caseSensitive),
    };
  }

  if (searchMode === 'name') {
    return {
      nameRanges: getMatchRanges(document.name, query, caseSensitive),
      pathRanges: [],
    };
  }

  return {
    nameRanges: getMatchRanges(document.name, query, caseSensitive),
    pathRanges: getMatchRanges(document.path, query, caseSensitive),
  };
}
```

```typescript
// ❌ 避免 - 复杂的三元运算符
const result = condition1 
  ? condition2 
    ? valueA 
    : valueB 
  : condition3 
    ? valueC 
    : valueD;

// ❌ 避免 - 嵌套三元运算符
const displayName = user 
  ? user.name 
    ? user.name 
    : user.email 
  : 'Guest';

// ✅ 好的示例 - 使用 if/else
let result: string;
if (condition1 && condition2) {
  result = valueA;
} else if (condition1) {
  result = valueB;
} else if (condition3) {
  result = valueC;
} else {
  result = valueD;
}

// ✅ 好的示例 - 提前返回
function getDisplayName(user: User | null): string {
  if (!user) {
    return 'Guest';
  }
  if (user.name) {
    return user.name;
  }
  return user.email;
}

// ✅ 好的示例 - 简单的三元运算符（单行）
const status = isActive ? 'active' : 'inactive';
```

### 10.2 减少空值合并运算符使用
**非必要不使用 `??`，优先使用显式判断或 lodash 工具函数。**

#### 10.2.1 默认值必须与 TypeScript 类型一致（强制）
**是否给默认值，必须由 TypeScript 类型决定：**

- **可选字段（`foo?: T` 或 `foo: T | undefined`）**：表示“可以没有”。**禁止为了方便而写默认值**（例如 `?? false` / `?? ''` / `?? SOME_DEFAULT`），应保持 `undefined` 语义并向下传递。
- **必填字段（`foo: T`）**：表示“必须有”。如果运行时可能缺失，应该**先调整类型为可选**，再在边界处（入口/解析/normalize）明确补齐默认值，并在返回类型上体现“已补齐”。

```typescript
type SearchMode = 'name' | 'path';
const SEARCH_MODE_NAME_OR_PATH: SearchMode = 'name';

type SearchOptionsInput = {
  caseSensitive?: boolean;
  mode?: SearchMode;
  root: string; // 必填：调用方必须提供
};

type SearchOptions = {
  caseSensitive?: boolean;
  mode?: SearchMode;
  root: string;
};

// ✅ 好的示例：可选字段保持 undefined，不强行默认
function buildSearchOptions(options: SearchOptionsInput): SearchOptions {
  return {
    caseSensitive: options.caseSensitive,
    mode: options.mode,
    root: options.root,
  };
}

type NormalizedSearchOptions = {
  caseSensitive: boolean;
  mode: SearchMode;
  root: string;
};

// ✅ 好的示例：在 normalize 边界补齐默认值，并用返回类型表达“已补齐”
function normalizeSearchOptions(options: SearchOptionsInput): NormalizedSearchOptions {
  return {
    caseSensitive: options.caseSensitive ?? false,
    mode: options.mode ?? SEARCH_MODE_NAME_OR_PATH,
    root: options.root,
  };
}

// ❌ 避免：字段是可选却到处默认，掩盖“没传”这个语义
function bad(options: SearchOptionsInput): NormalizedSearchOptions {
  return {
    caseSensitive: options.caseSensitive ?? false,
    mode: options.mode ?? SEARCH_MODE_NAME_OR_PATH,
    root: options.root,
  };
}
```

```typescript
// ❌ 避免 - 过度使用 ??
const name = user.name ?? 'Unknown';
const age = user.age ?? 18;
const email = user.email ?? '';

// ❌ 避免 - 链式使用 ??
const value = a ?? b ?? c ?? d ?? 'default';

// ❌ 严禁 - 使用 `?? null` 作为默认值（没有就是没有）
// 语义上“没有值”就应该保持 `undefined`/`null` 的原始状态，而不是再默认成 `null`
const invalid = user.nickname ?? null;

// ❌ 严禁 - 使用 `?? false` / `?? 0` / `?? ''` 等兜底掩盖“没有这个字段”
// 是否存在、是否开启功能应该由类型本身表达，不允许用布尔/数字/字符串默认值来假装“有个关掉的值”
const capabilities = {
  streaming: validated.capabilities?.streaming ?? false,
  functionCalling: validated.capabilities?.functionCalling ?? false,
  vision: validated.capabilities?.vision ?? false,
  jsonMode: validated.capabilities?.jsonMode ?? false,
};

// ✅ 好的示例 - 使用显式判断
let name: string;
if (user.name) {
  name = user.name;
} else {
  name = 'Unknown';
}

// ✅ 好的示例 - 使用 lodash 的 defaultTo
import { defaultTo } from 'lodash-es';

const name = defaultTo(user.name, 'Unknown');
const age = defaultTo(user.age, 18);

// ✅ 好的示例 - 直接用类型表达“有没有”，不做 `?? false` 兜底
type Capabilities = {
  streaming?: boolean;
  functionCalling?: boolean;
  vision?: boolean;
  jsonMode?: boolean;
};

function normalizeCapabilities(validated: { capabilities?: Capabilities }): Capabilities {
  return {
    streaming: validated.capabilities?.streaming,
    functionCalling: validated.capabilities?.functionCalling,
    vision: validated.capabilities?.vision,
    jsonMode: validated.capabilities?.jsonMode,
  };
}

// ✅ 好的示例 - 提前返回
function getUserName(user: User | null): string {
  if (!user) {
    return 'Unknown';
  }
  return user.name;
}

// ✅ 好的示例 - 保持未赋值状态而不是强行默认为 null
type Nickname = string | undefined;

function getNickname(user: User): Nickname {
  if (!user.nickname) {
    return undefined;
  }
  return user.nickname;
}
```

### 10.3 逻辑判断规范
```typescript
// ❌ 避免 - 复杂的布尔表达式
if ((a && b) || (c && !d) || (e && f && g)) {
  // ...
}

// ✅ 好的示例 - 提取为变量或函数
const isValidUser = a && b;
const hasPermission = c && !d;
const isAuthorized = e && f && g;

if (isValidUser || hasPermission || isAuthorized) {
  // ...
}

// ✅ 更好的示例 - 提取为函数
function canAccessResource(user: User): boolean {
  if (!user.isActive) {
    return false;
  }
  if (!user.hasPermission('read')) {
    return false;
  }
  return true;
}

if (canAccessResource(user)) {
  // ...
}
```

## 12. ESLint 配置

### 11.1 推荐配置 (.eslintrc.json)
```json
{
  "extends": [
    "eslint:recommended",
    "@typescript-eslint/recommended",
    "@typescript-eslint/recommended-requiring-type-checking"
  ],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "project": "./tsconfig.json"
  },
  "plugins": ["@typescript-eslint"],
  "rules": {
    "@typescript-eslint/explicit-function-return-type": "off",
    "@typescript-eslint/no-unused-vars": ["error", { "argsIgnorePattern": "^_" }],
    "@typescript-eslint/no-explicit-any": "error",
    "prefer-const": "error",
    "no-var": "error",
    // 禁止 typeof 类型检查
    "@typescript-eslint/no-typeof-undefined": "error",
    // 最大文件行数限制
    "max-lines": ["error", { "max": 400, "skipBlankLines": true, "skipComments": true }],
    // 禁止短变量名
    "id-length": ["error", { "min": 2, "exceptions": ["i", "j", "k", "x", "y", "z"] }],
    // 禁止特定标识符
    "id-denylist": ["error", "data", "info", "item", "value", "temp", "obj", "arr"]
  }
}
```

### 11.2 自定义 ESLint 规则 - 禁止 "is" 前缀
创建自定义规则文件 `.eslint/rules/no-is-prefix.js`：

```javascript
module.exports = {
  meta: {
    type: 'suggestion',
    docs: {
      description: 'Disallow "is" prefix in identifiers',
      category: 'Best Practices',
      recommended: true,
    },
    schema: [],
    messages: {
      noIsPrefix: 'Avoid "is" prefix in "{{name}}". Use alternatives like "has", "should", "can", or descriptive boolean phrases.',
    },
  },
  create(context) {
    function checkIdentifier(node) {
      const name = node.name;
      if (name.startsWith('is') && name.length > 2 && name[2] === name[2].toUpperCase()) {
        context.report({
          node,
          messageId: 'noIsPrefix',
          data: { name },
        });
      }
    }

    return {
      Identifier: checkIdentifier,
      FunctionDeclaration(node) {
        if (node.id) checkIdentifier(node.id);
      },
      ClassDeclaration(node) {
        if (node.id) checkIdentifier(node.id);
      },
      TSInterfaceDeclaration(node) {
        if (node.id) checkIdentifier(node.id);
      },
      VariableDeclarator(node) {
        if (node.id.type === 'Identifier') {
          checkIdentifier(node.id);
        }
      },
    };
  },
};
```

### 11.3 自定义 ESLint 规则 - 禁止硬编码
创建自定义规则文件 `.eslint/rules/no-magic-values.js`：

```javascript
module.exports = {
  meta: {
    type: 'suggestion',
    docs: {
      description: 'Disallow magic values (hardcoded literals)',
      category: 'Best Practices',
      recommended: true,
    },
    schema: [
      {
        type: 'object',
        properties: {
          ignore: {
            type: 'array',
            items: { type: 'string' },
          },
          // 允许在测试文件中直接使用字面量
          ignoreInTestFiles: {
            type: 'boolean',
            default: true,
          },
        },
      },
    ],
    messages: {
      noMagic: 'Unexpected magic value "{{value}}". Consider using a named constant or adding a comment to explain its meaning.',
    },
  },
  create(context) {
    const options = context.options[0] || {};
    const ignore = options.ignore || ['0', '1', '-1', 'true', 'false'];
    const ignoreInTestFiles = options.ignoreInTestFiles !== false;
    const filename = context.getFilename();
    
    // 检查是否在测试文件中
    const isTestFile = /\.(test|spec)\.(ts|tsx|js|jsx)$/.test(filename);
    if (ignoreInTestFiles && isTestFile) {
      return {};
    }

    return {
      Literal(node) {
        // 忽略数字 0, 1, -1 和布尔值
        if (ignore.includes(String(node.value))) {
          return;
        }

        // 检查字符串字面量（排除空字符串，由其他规则处理）
        if (typeof node.value === 'string' && node.value !== '') {
          // 允许短字符串（如 key 值）
          if (node.value.length <= 3) {
            return;
          }
          // 允许标准 HTTP 方法
          if (['GET', 'POST', 'PUT', 'DELETE', 'PATCH'].includes(node.value)) {
            return;
          }
          // 允许标准 Content-Type
          if (node.value.startsWith('application/') || node.value.startsWith('text/')) {
            return;
          }
          context.report({
            node,
            messageId: 'noMagic',
            data: { value: node.value },
          });
        }

        // 检查数字字面量
        if (typeof node.value === 'number' && node.value !== 0 && node.value !== 1) {
          // 允许标准 HTTP 状态码
          if ([200, 201, 204, 400, 401, 403, 404, 500].includes(node.value)) {
            return;
          }
          // 允许时间相关的常见值（毫秒）
          if ([100, 200, 300, 500, 1000, 2000, 5000, 10000, 60000, 300000, 600000].includes(node.value)) {
            return;
          }
          // 允许百分比相关的值
          if ([100, 50, 25, 75, 10, 20, 30, 40, 60, 70, 80, 90].includes(node.value)) {
            return;
          }
          context.report({
            node,
            messageId: 'noMagic',
            data: { value: node.value },
          });
        }
      },
    };
  },
};
```

**注意**：此 ESLint 规则是辅助工具，不是绝对限制。开发者应根据实际场景判断是否使用常量，参考上面的"必须使用常量的场景"和"可以直接使用字面量的场景"。

### 11.4 自定义 ESLint 规则 - 禁止空字符串
创建自定义规则文件 `.eslint/rules/no-empty-string.js`：

```javascript
module.exports = {
  meta: {
    type: 'problem',
    docs: {
      description: 'Disallow empty string literals',
      category: 'Best Practices',
      recommended: true,
    },
    schema: [],
    messages: {
      noEmptyString: 'Empty string literals are not allowed. Use null or undefined instead.',
    },
  },
  create(context) {
    return {
      Literal(node) {
        if (node.value === '') {
          context.report({
            node,
            messageId: 'noEmptyString',
          });
        }
      },
    };
  },
};
```

### 11.5 使用 ESLint 插件配置
```javascript
// .eslintrc.js
const noIsPrefixRule = require('./.eslint/rules/no-is-prefix');
const noMagicValuesRule = require('./.eslint/rules/no-magic-values');
const noEmptyStringRule = require('./.eslint/rules/no-empty-string');

module.exports = {
  // ... 其他配置
  rules: {
    // ... 其他规则
    'no-is-prefix': 'error',
    'no-magic-values': 'error',
    'no-empty-string': 'error',
  },
  plugins: [
    {
      rules: {
        'no-is-prefix': noIsPrefixRule,
        'no-magic-values': noMagicValuesRule,
        'no-empty-string': noEmptyStringRule,
      },
    },
  ],
};
```

## 13. 注释规范（参考 Google）

### 12.1 注释原则
- **注释应该解释 "为什么" 而不是 "做什么"**
- **代码本身应该清晰表达 "做什么"**
- **保持注释简洁、准确、有用**
- **及时更新注释，删除过时的注释**

### 12.2 文件头注释
```typescript
/**
 * @fileoverview 用户服务模块，处理用户相关的业务逻辑
 * @description 提供用户 CRUD、认证、授权等功能
 * @module services/user
 * @author Your Name
 * @since 1.0.0
 */

// 或者使用更简洁的格式
/**
 * 用户服务 - 处理用户认证和授权
 * @module services/user
 */
```

### 12.3 JSDoc 注释规范

#### 函数/方法注释
```typescript
/**
 * 根据用户 ID 获取用户信息
 * @param {string} userId - 用户唯一标识符
 * @param {boolean} [includeProfile=false] - 是否包含用户详细资料
 * @returns {Promise<User | null>} 用户对象，如果未找到则返回 null
 * @throws {ValidationError} 当 userId 格式无效时抛出
 * @example
 * const user = await getUser('user-123');
 * const userWithProfile = await getUser('user-123', true);
 */
async function getUser(
  userId: string,
  includeProfile: boolean = false
): Promise<User | null> {
  // 实现
}

/**
 * 计算订单总价，包含税费和折扣
 * @param {OrderItem[]} items - 订单商品列表
 * @param {TaxRate} taxRate - 税率配置
 * @param {string} [couponCode] - 优惠券代码（可选）
 * @returns {OrderSummary} 订单汇总信息
 */
function calculateOrderTotal(
  items: OrderItem[],
  taxRate: TaxRate,
  couponCode?: string
): OrderSummary {
  // 实现
}
```

#### 类和接口注释
```typescript
/**
 * 用户服务类，封装用户相关的所有业务逻辑
 * @implements {IUserService}
 */
class UserService implements IUserService {
  /** 用户数据缓存 */
  private cache: Map<string, User>;

  /**
   * 创建用户服务实例
   * @param {ApiClient} apiClient - API 客户端
   * @param {Logger} logger - 日志记录器
   */
  constructor(
    private apiClient: ApiClient,
    private logger: Logger
  ) {
    this.cache = new Map();
  }

  /**
   * 根据 ID 获取用户
   * @param {string} id - 用户 ID
   * @returns {Promise<User | null>} 用户对象或 null
   */
  async getUserById(id: string): Promise<User | null> {
    // 实现
  }
}

/**
 * 用户实体接口
 * @interface
 */
interface User {
  /** 用户唯一标识符 */
  id: string;
  /** 用户显示名称 */
  name: string;
  /** 用户邮箱地址 */
  email: string;
  /** 用户创建时间 */
  readonly createdAt: Date;
}
```

#### 复杂类型注释
```typescript
/**
 * 分页查询结果
 * @template T - 数据项类型
 */
interface PaginatedResult<T> {
  /** 数据列表 */
  data: T[];
  /** 总记录数 */
  total: number;
  /** 当前页码 */
  page: number;
  /** 每页大小 */
  pageSize: number;
  /** 是否有下一页 */
  hasNextPage: boolean;
}

/**
 * API 响应状态码
 * @readonly
 * @enum {number}
 */
enum ApiStatusCode {
  /** 请求成功 */
  SUCCESS = 200,
  /** 创建成功 */
  CREATED = 201,
  /** 请求参数错误 */
  BAD_REQUEST = 400,
  /** 未授权 */
  UNAUTHORIZED = 401,
  /** 禁止访问 */
  FORBIDDEN = 403,
  /** 资源不存在 */
  NOT_FOUND = 404,
  /** 服务器内部错误 */
  SERVER_ERROR = 500
}
```

### 12.4 行内注释规范

#### 何时使用行内注释
```typescript
// ✅ 好的示例 - 解释复杂的业务逻辑
function calculateTax(amount: number, region: string): number {
  // 某些地区对数字服务免税
  if (isDigitalServiceExempt(region)) {
    return 0;
  }

  // 税率根据地区动态计算
  const rate = getTaxRate(region);
  return amount * rate;
}

// ✅ 好的示例 - 标记 TODO 或 FIXME
// TODO: 添加缓存机制，避免重复计算
function expensiveOperation(data: Data): Result {
  // ...
}

// FIXME: 临时解决方案，需要重构
function temporaryFix(): void {
  // ...
}

// ✅ 好的示例 - 解释非显而易见的代码
// 使用位运算检查权限，比数组查找更高效
const hasPermission = (user.permissions & Permission.ADMIN) !== 0;
```

#### 禁止的注释写法
```typescript
// ❌ 禁止 - 显而易见的注释
// 初始化 count 为 0
let count = 0;

// ❌ 禁止 - 注释掉的代码
// function oldFunction() { }

// ❌ 禁止 - 误导性注释
// 返回用户列表（实际返回的是订单列表）
function getOrders() { }

// ❌ 禁止 - 过时的注释
// 注意：此函数在 v1.0 中有性能问题（已在 v2.0 修复）
function optimizedFunction() { }
```

### 12.5 特殊注释标记
```typescript
// TODO: 需要实现的功能
// FIXME: 需要修复的问题
// HACK: 临时解决方案，需要重构
// NOTE: 重要的实现细节说明
// REVIEW: 需要代码审查的地方
// DEPRECATED: 已弃用的代码，将在未来版本移除
// WARNING: 潜在的风险或副作用

// 示例
// HACK: 由于第三方库的限制，暂时使用这种方式处理
// 后续需要升级到 v3.0 后重构
function workaround() { }

// NOTE: 这个顺序很重要，不能随意更改
const PROCESSING_ORDER = [
  'validation',
  'transformation',
  'enrichment',
  'persistence'
];
```

## 14. 代码审查清单

### 13.1 命名规范检查
- [ ] 没有以 "is" 开头的变量、函数、类、接口名
- [ ] 没有使用无意义的通用词（data, info, item, value, temp, obj, arr）
- [ ] 没有使用含糊的 id 命名（id, ID, providerId, sourceId 等），ID 必须带上清晰的领域前缀/后缀（如 userAccountId, paymentProviderId 等）
- [ ] 没有使用任何缩写（含缩写变量名、函数名、类名等）
- [ ] 没有以 Data 结尾的命名（userData, listData, formData, tableData 等）
- [ ] 没有使用匈牙利命名法
- [ ] 布尔值使用描述性复合名称
- [ ] 函数使用动作导向动词
- [ ] 类和接口使用反映领域概念的名词短语

### 13.2 文件大小检查
- [ ] 单个文件不超过 400 行代码
- [ ] 超过 400 行的文件已拆分为多个模块
- [ ] 每个文件职责单一

### 13.3 类型定义检查
- [ ] 所有类型在独立文件中定义
- [ ] 没有在代码中内联定义类型
- [ ] 没有使用 typeof 进行类型检查

### 13.4 空字符串检查
- [ ] 没有定义空字符串作为变量值
- [ ] 没有使用空字符串作为默认值
- [ ] 使用 null 或 undefined 代替空字符串

### 13.5 条件表达式检查
- [ ] 复杂条件不用嵌套三元运算符，优先使用 `if/else` 或提前返回
- [ ] 非必要不使用 `??`，优先使用显式判断或 lodash 工具函数
- [ ] 严禁使用 `?? null` 作为默认值（没有就是没有）

### 13.6 硬编码检查
- [ ] 业务状态值使用常量（用户状态、订单状态等）
- [ ] 配置参数使用常量（超时时间、重试次数、分页大小等）
- [ ] 外部接口标识使用常量（API 地址、错误码等）
- [ ] 多处使用的值提取为常量
- [ ] 可能变化的值使用常量
- [ ] 语义不明确的字面量添加注释说明
- [ ] 常量按功能分类存放在 constants 目录

### 13.7 模块导入检查
- [ ] 导入顺序符合规范（内置 → 外部 → 内部绝对 → 内部相对 → 类型）
- [ ] 没有深层相对路径导入（超过两级）
- [ ] 使用路径别名而非相对路径
- [ ] 类型导入使用 `import type`

### 13.8 React Hooks 检查
- [ ] Hooks 只在组件顶层调用
- [ ] useEffect 依赖数组完整
- [ ] 自定义 Hooks 以 use 开头
- [ ] useCallback/useMemo 使用合理

### 13.9 注释规范检查
- [ ] 复杂函数有 JSDoc 注释
- [ ] 注释解释 "为什么" 而非 "做什么"
- [ ] 没有过时的注释
- [ ] 没有注释掉的代码

## 15. 代码格式化规范（参考 StandardJS + Prettier）

### 14.1 缩进和空格
```typescript
// ✅ 使用 2 个空格缩进（不使用 Tab）
function example() {
  if (condition) {
    doSomething();
  }
}

// ✅ 运算符前后加空格
const sum = a + b;
const result = x > 0 ? x : 0;

// ✅ 逗号后加空格
const arr = [1, 2, 3];
const obj = { a: 1, b: 2 };

// ❌ 避免 - 多余空格
const x= 1;
const y =2;
```

### 14.2 分号规范
```typescript
// ✅ 使用分号结束语句
const name = 'John';
const age = 30;

function greet() {
  return 'Hello';
}

// ❌ 避免 - 省略分号
const name = 'John'
const age = 30
```

### 14.3 引号规范
```typescript
// ✅ 使用单引号
const message = 'Hello World';
const template = `Hello ${name}`;

// ✅ HTML 属性使用双引号
const element = <div className="container">Content</div>;

// ❌ 避免 - 混用引号
const bad = "inconsistent";
```

### 14.4 大括号规范
```typescript
// ✅ 控制语句使用大括号
if (condition) {
  doSomething();
}

// ✅ 对象/函数大括号不换行
const obj = {
  key: 'value'
};

function example() {
  return true;
}

// ❌ 避免 - 省略大括号
if (condition) doSomething();

// ❌ 避免 - 大括号换行（Allman 风格）
function bad()
{
  return true;
}
```

### 14.5 行长度和换行
```typescript
// ✅ 最大行长度 100 字符
// 超过长度时合理换行
const longString = 'This is a very long string that needs to be ' +
  'broken into multiple lines for better readability';

// ✅ 函数参数换行
function processData(
  userId: string,
  options: ProcessOptions,
  callback: CallbackFn
): Promise<Result> {
  // ...
}

// ✅ 链式调用换行
const result = data
  .filter(item => item.active)
  .map(item => item.name)
  .sort()
  .slice(0, 10);

// ✅ 对象属性换行
const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3,
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${token}`
  }
};
```

### 14.6 空行规范
```typescript
// ✅ 文件末尾保留一个空行

// ✅ 逻辑块之间加空行
function processUser(user: User) {
  const normalized = normalizeUser(user);

  if (!normalized.isValid) {
    throw new Error('Invalid user');
  }

  const saved = await saveUser(normalized);
  return saved;
}

// ✅ 类成员之间加空行
class UserService {
  private cache: Map<string, User>;

  constructor() {
    this.cache = new Map();
  }

  async getUser(id: string): Promise<User> {
    // ...
  }

  async saveUser(user: User): Promise<void> {
    // ...
  }
}
```

### 14.7 括号空格
```typescript
// ✅ 函数调用括号内不加空格
foo(arg1, arg2);

// ✅ 函数定义括号内不加空格
function foo(arg1, arg2) { }

// ✅ 对象/数组括号内不加空格
const obj = { key: 'value' };
const arr = [1, 2, 3];

// ❌ 避免 - 括号内加空格
foo( arg1, arg2 );
const obj = { key: 'value' };
```

### 14.8 Prettier 配置推荐
```json
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "bracketSpacing": true,
  "arrowParens": "avoid",
  "endOfLine": "lf",
  "jsxSingleQuote": false,
  "jsxBracketSameLine": false
}
```

### 14.9 与 ESLint 集成
```json
// .eslintrc.json
{
  "extends": [
    "eslint:recommended",
    "@typescript-eslint/recommended",
    "plugin:react-hooks/recommended",
    "prettier" // 必须放在最后，覆盖其他配置的格式规则
  ],
  "plugins": ["@typescript-eslint", "react-hooks"],
  "rules": {
    // 格式相关规则由 Prettier 处理，ESLint 关闭
    "indent": "off",
    "quotes": "off",
    "semi": "off",
    "comma-dangle": "off",
    "max-len": "off"
  }
}
```

## 16. 工具推荐

- **TypeScript**: 类型检查
- **ESLint**: 代码质量检查
- **Prettier**: 代码格式化
- **Husky**: Git 钩子
- **lint-staged**: 暂存文件检查
- **lodash-es**: 工具函数库
- **destr**: 安全、快速的 JSON.parse 替代方案
- **eslint-config-airbnb**: Airbnb 规范配置
- **eslint-config-prettier**: 关闭与 Prettier 冲突的 ESLint 规则
- **eslint-plugin-react-hooks**: React Hooks 规则检查
