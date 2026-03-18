# 项目结构规范

## 1. 通用项目结构

### 1.1 基础目录结构
```
project-name/
├── README.md                 # 项目说明文档
├── LICENSE                   # 许可证文件
├── .gitignore               # Git 忽略文件
├── .editorconfig            # 编辑器配置
├── .gitattributes           # Git 属性配置
│
├── docs/                    # 文档目录
│   ├── architecture.md      # 架构文档
│   ├── api/                 # API 文档
│   ├── deployment.md        # 部署文档
│   └── development.md       # 开发文档
│
├── config/                  # 配置文件目录
│   ├── dev/                 # 开发环境配置
│   ├── test/                # 测试环境配置
│   └── prod/                # 生产环境配置
│
├── scripts/                 # 脚本目录
│   ├── setup.sh             # 初始化脚本
│   ├── build.sh             # 构建脚本
│   ├── test.sh              # 测试脚本
│   └── deploy.sh            # 部署脚本
│
├── src/                     # 源代码目录
│   └── ...
│
├── tests/                   # 测试目录
│   └── ...
│
├── tools/                   # 工具目录
│   └── ...
│
└── assets/                  # 资源文件目录
    └── ...
```

### 1.2 通用文件模板

#### .gitignore
```gitignore
# Dependencies
node_modules/
vendor/
__pycache__/
*.pyc

# Build outputs
dist/
build/
target/
*.exe
*.dll
*.so

# IDE
.idea/
.vscode/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Logs
logs/
*.log
npm-debug.log*

# Environment
.env
.env.local
.env.*.local

# Testing
coverage/
.nyc_output/

# Temporary files
tmp/
temp/
*.tmp
```

#### .editorconfig
```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.{js,ts,jsx,tsx,json,yml,yaml}]
indent_style = space
indent_size = 2

[*.{py,java}]
indent_style = space
indent_size = 4

[*.md]
trim_trailing_whitespace = false

[Makefile]
indent_style = tab
```

## 2. Web 前端项目结构

### 2.1 React/Vue/Angular 项目
```
my-web-app/
├── public/                  # 静态资源
│   ├── index.html
│   ├── favicon.ico
│   └── assets/             # 不需要构建的资源
│
├── src/
│   ├── main.tsx            # 应用入口
│   ├── App.tsx             # 根组件
│   │
│   ├── components/         # 通用组件
│   │   ├── common/         # 基础组件
│   │   │   ├── Button/
│   │   │   │   ├── index.tsx
│   │   │   │   ├── Button.tsx
│   │   │   │   ├── Button.test.tsx
│   │   │   │   └── Button.module.css
│   │   │   ├── Input/
│   │   │   └── Modal/
│   │   └── business/       # 业务组件
│   │       ├── UserCard/
│   │       └── ProductList/
│   │
│   ├── plugins/            # 插件统一目录（项目级扩展/能力注入）
│   │   ├── index.ts         # 插件注册入口（按需）
│   │   └── ...              # 例如：i18n/analytics/error-tracking 等
│   │
│   ├── routers/            # 路由统一目录
│   │   ├── index.ts         # 路由入口（聚合导出/注册）
│   │   └── ...              # 例如：routes.ts、guards.ts、loaders.ts
│   │
│   ├── pages/              # 页面统一目录（每个页面必须是“目录”）
│   │   ├── home/
│   │   │   ├── index.tsx            # 页面入口（该页面对外唯一入口）
│   │   │   ├── page.tsx             # 页面实现（可选命名）
│   │   │   ├── page.test.tsx        # 页面测试（可选）
│   │   │   └── components/          # 页面私有组件（禁止被其它页面/模块复用）
│   │   │       └── ...
│   │   ├── about/
│   │   │   └── index.tsx
│   │   └── user/                    # 支持子目录（页面分组/子路由）
│   │       ├── profile/
│   │       │   └── index.tsx
│   │       └── settings/
│   │           └── index.tsx
│   │
│   ├── hooks/              # 自定义 Hooks
│   │   ├── useAuth.ts
│   │   ├── useLocalStorage.ts
│   │   └── useFetch.ts
│   │
│   ├── stores/             # 状态管理
│   │   ├── index.ts
│   │   ├── userStore.ts
│   │   └── appStore.ts
│   │
│   ├── services/           # API 服务
│   │   ├── api/
│   │   │   ├── client.ts
│   │   │   ├── userApi.ts
│   │   │   └── productApi.ts
│   │   └── types/
│   │       ├── user.ts
│   │       └── product.ts
│   │
│   ├── utils/              # 工具函数
│   │   ├── format.ts
│   │   ├── validate.ts
│   │   └── constants.ts
│   │
│   ├── styles/             # 全局样式
│   │   ├── global.css
│   │   ├── variables.css
│   │   └── mixins.scss
│   │
│   ├── types/              # 全局类型定义
│   │   ├── global.ts
│   │   └── api.ts
│   │
│   └── assets/             # 需要构建的资源
│       ├── images/
│       ├── icons/
│       └── fonts/
│
├── tests/                  # 测试文件
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── .env                    # 环境变量
├── .env.development
├── .env.production
├── vite.config.ts          # 构建配置
├── tsconfig.json
├── package.json
└── README.md
```

### 2.2 前端目录约定（必须遵守）

#### 2.2.1 `plugins/`（插件统一目录）
- **统一入口**：项目内的“插件/扩展/能力注入”代码统一放在 `src/plugins/`。
- **典型内容**：埋点/分析、国际化、错误监控、权限初始化、全局拦截器、第三方 SDK 初始化等。
- **禁止**：不要把插件初始化散落在 `src/utils/`、`src/services/` 或某个页面目录里。

#### 2.2.2 `routers/`（路由统一目录）
- **统一入口**：路由定义、路由守卫、路由数据加载等统一放在 `src/routers/`。
- **页面解耦**：页面只负责 UI 与页面内逻辑；路由注册/拼装在 `routers/` 完成。

#### 2.2.3 `pages/`（页面统一目录：一页一目录）
- **页面=目录**：`src/pages/` 下每个页面必须是一个目录（禁止出现单文件页面 `src/pages/Foo.tsx`）。
- **入口文件**：每个页面目录必须包含 `index.tsx`/`index.ts` 作为**该页面对外唯一入口**。
- **页面私有组件**：页面专用组件必须放在 `src/pages/<page>/components/`，并且**禁止跨页面复用**。
  - 需要跨页面复用的组件，提升到 `src/components/`（通用组件）或更合适的共享目录。
- **支持子目录**：允许 `src/pages/<group>/<page>/index.tsx` 形式的子目录，用于页面分组或子路由。
- **命名**：页面目录、子目录统一使用**小写连字符**（例如 `user-settings/`），与全局文件/目录命名规范一致。

### 2.2 组件文件结构
```
ComponentName/
├── index.ts                # 统一导出
├── ComponentName.tsx       # 组件实现
├── ComponentName.test.tsx  # 单元测试
├── ComponentName.module.css # 样式（CSS Modules）
├── ComponentName.stories.tsx # Storybook 文档
└── types.ts                # 组件类型定义
```

## 3. Node.js 后端项目结构

### 3.1 Express/NestJS 项目
```
my-api/
├── src/
│   ├── main.ts             # 应用入口
│   ├── app.module.ts       # 根模块（NestJS）
│   │
│   ├── config/             # 配置
│   │   ├── database.ts
│   │   ├── redis.ts
│   │   └── app.config.ts
│   │
│   ├── common/             # 通用模块
│   │   ├── decorators/     # 装饰器
│   │   ├── filters/        # 异常过滤器
│   │   ├── guards/         # 守卫
│   │   ├── interceptors/   # 拦截器
│   │   ├── pipes/          # 管道
│   │   └── utils/          # 工具函数
│   │
│   ├── modules/            # 业务模块
│   │   ├── users/
│   │   │   ├── users.module.ts
│   │   │   ├── users.controller.ts
│   │   │   ├── users.service.ts
│   │   │   ├── users.repository.ts
│   │   │   ├── dto/
│   │   │   │   ├── create-user.dto.ts
│   │   │   │   └── update-user.dto.ts
│   │   │   ├── entities/
│   │   │   │   └── user.entity.ts
│   │   │   └── tests/
│   │   │       ├── users.service.spec.ts
│   │   │       └── users.controller.spec.ts
│   │   ├── auth/
│   │   ├── products/
│   │   └── orders/
│   │
│   ├── database/           # 数据库
│   │   ├── migrations/
│   │   └── seeds/
│   │
│   ├── shared/             # 共享模块
│   │   ├── services/
│   │   └── providers/
│   │
│   └── types/              # 全局类型
│       └── index.ts
│
├── test/                   # 测试
│   ├── jest-e2e.json
│   └── e2e/
│
├── prisma/                 # ORM 配置（Prisma）
│   ├── schema.prisma
│   └── migrations/
│
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
│
├── scripts/
│   ├── start.sh
│   └── migrate.sh
│
├── .env
├── .env.example
├── nest-cli.json
├── tsconfig.json
└── package.json
```

## 4. Python 项目结构

### 4.1 Flask/FastAPI/Django 项目
```
my-python-project/
├── pyproject.toml          # 项目配置（PEP 518）
├── setup.py               # 包配置
├── requirements/
│   ├── base.txt
│   ├── dev.txt
│   └── prod.txt
├── .env
├── .env.example
│
├── src/                   # 源代码
│   └── myproject/
│       ├── __init__.py
│       ├── main.py        # 应用入口
│       │
│       ├── config/        # 配置
│       │   ├── __init__.py
│       │   ├── settings.py
│       │   └── development.py
│       │
│       ├── api/           # API 层
│       │   ├── __init__.py
│       │   ├── v1/
│       │   │   ├── __init__.py
│       │   │   ├── users.py
│       │   │   └── products.py
│       │   └── deps.py    # 依赖注入
│       │
│       ├── models/        # 数据模型
│       │   ├── __init__.py
│       │   ├── user.py
│       │   └── product.py
│       │
│       ├── schemas/       # Pydantic 模型
│       │   ├── __init__.py
│       │   ├── user.py
│       │   └── product.py
│       │
│       ├── services/      # 业务逻辑
│       │   ├── __init__.py
│       │   ├── user_service.py
│       │   └── product_service.py
│       │
│       ├── repositories/  # 数据访问
│       │   ├── __init__.py
│       │   ├── user_repo.py
│       │   └── product_repo.py
│       │
│       ├── core/          # 核心功能
│       │   ├── __init__.py
│       │   ├── security.py
│       │   ├── exceptions.py
│       │   └── utils.py
│       │
│       ├── db/            # 数据库
│       │   ├── __init__.py
│       │   ├── session.py
│       │   └── base.py
│       │
│       └── tasks/         # 后台任务
│           ├── __init__.py
│           └── email_tasks.py
│
├── tests/                 # 测试
│   ├── __init__.py
│   ├── conftest.py        # pytest 配置
│   ├── unit/
│   │   ├── __init__.py
│   │   ├── test_users.py
│   │   └── test_products.py
│   ├── integration/
│   └── e2e/
│
├── alembic/               # 数据库迁移
│   ├── versions/
│   └── env.py
│
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
│
├── scripts/
│   ├── run.sh
│   ├── test.sh
│   └── migrate.sh
│
├── docs/                  # 文档
│   └── api.md
│
└── README.md
```

## 5. Java 项目结构

### 5.1 Spring Boot 项目
```
my-spring-app/
├── pom.xml                # Maven 配置
├── mvnw
├── mvnw.cmd
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── myapp/
│   │   │               ├── Application.java
│   │   │               │
│   │   │               ├── config/          # 配置类
│   │   │               │   ├── SecurityConfig.java
│   │   │               │   ├── WebConfig.java
│   │   │               │   └── DatabaseConfig.java
│   │   │               │
│   │   │               ├── controller/      # 控制器层
│   │   │               │   ├── UserController.java
│   │   │               │   └── ProductController.java
│   │   │               │
│   │   │               ├── service/         # 服务层
│   │   │               │   ├── UserService.java
│   │   │               │   ├── ProductService.java
│   │   │               │   └── impl/
│   │   │               │       ├── UserServiceImpl.java
│   │   │               │       └── ProductServiceImpl.java
│   │   │               │
│   │   │               ├── repository/      # 数据访问层
│   │   │               │   ├── UserRepository.java
│   │   │               │   └── ProductRepository.java
│   │   │               │
│   │   │               ├── entity/          # 实体类
│   │   │               │   ├── User.java
│   │   │               │   └── Product.java
│   │   │               │
│   │   │               ├── dto/             # 数据传输对象
│   │   │               │   ├── UserDTO.java
│   │   │               │   ├── request/
│   │   │               │   └── response/
│   │   │               │
│   │   │               ├── mapper/          # 对象映射
│   │   │               │   ├── UserMapper.java
│   │   │               │   └── ProductMapper.java
│   │   │               │
│   │   │               ├── exception/       # 异常处理
│   │   │               │   ├── GlobalExceptionHandler.java
│   │   │               │   ├── BusinessException.java
│   │   │               │   └── ErrorCode.java
│   │   │               │
│   │   │               ├── security/        # 安全相关
│   │   │               │   ├── JwtTokenProvider.java
│   │   │               │   └── JwtAuthenticationFilter.java
│   │   │               │
│   │   │               ├── util/            # 工具类
│   │   │               │   ├── DateUtils.java
│   │   │               │   └── JsonUtils.java
│   │   │               │
│   │   │               └── constant/        # 常量
│   │   │                   ├── AppConstants.java
│   │   │                   └── ErrorMessages.java
│   │   │
│   │   └── resources/
│   │       ├── application.yml
│   │       ├── application-dev.yml
│   │       ├── application-prod.yml
│   │       ├── static/
│   │       └── templates/
│   │
│   └── test/
│       └── java/
│           └── com/
│               └── example/
│                   └── myapp/
│                       ├── unit/
│                       └── integration/
│
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
│
└── README.md
```

## 6. 文档项目结构

### 6.1 文档目录
```
docs/
├── README.md              # 文档首页
├── architecture/          # 架构文档
│   ├── overview.md
│   ├── system-design.md
│   └── data-flow.md
├── api/                   # API 文档
│   ├── authentication.md
│   ├── users.md
│   └── products.md
├── development/           # 开发文档
│   ├── setup.md
│   ├── coding-standards.md
│   └── testing.md
├── deployment/            # 部署文档
│   ├── environment-setup.md
│   ├── ci-cd.md
│   └── monitoring.md
├── operations/            # 运维文档
│   ├── runbook.md
│   └── troubleshooting.md
└── assets/                # 文档资源
    ├── images/
    └── diagrams/
```

## 7. 配置文件规范

### 7.1 环境配置
```
config/
├── application.yml        # 默认配置
├── application-dev.yml    # 开发环境
├── application-test.yml   # 测试环境
├── application-prod.yml   # 生产环境
└── application-local.yml  # 本地开发（不提交到版本控制）
```

### 7.2 配置优先级
1. 命令行参数
2. 环境变量
3. 配置文件（按环境）
4. 默认配置

## 8. 脚本规范

### 8.1 脚本目录
```
scripts/
├── setup.sh               # 项目初始化
├── build.sh               # 构建项目
├── test.sh                # 运行测试
├── lint.sh                # 代码检查
├── start.sh               # 启动服务
├── stop.sh                # 停止服务
├── deploy.sh              # 部署脚本
└── db/
    ├── migrate.sh         # 数据库迁移
    ├── rollback.sh        # 回滚迁移
    └── seed.sh            # 数据种子
```

### 8.2 脚本规范
- 使用统一的 shebang: `#!/bin/bash` 或 `#!/bin/sh`
- 添加错误处理: `set -e`
- 使用有意义的变量名
- 添加注释说明
- 提供使用帮助信息

```bash
#!/bin/bash
set -e

# 脚本说明：构建项目
# 使用方法: ./scripts/build.sh [dev|prod]

ENV=${1:-dev}
echo "Building for environment: $ENV"

# 构建逻辑...
```

## 9. 测试目录结构

### 9.1 测试组织
```
tests/
├── unit/                  # 单元测试
│   ├── components/
│   ├── services/
│   └── utils/
├── integration/           # 集成测试
│   ├── api/
│   └── database/
├── e2e/                   # 端到端测试
│   └── scenarios/
├── fixtures/              # 测试数据
│   ├── users.json
│   └── products.json
├── helpers/               # 测试辅助函数
│   ├── setup.ts
│   └── utils.ts
└── __mocks__/             # Mock 数据
    └── api.ts
```

## 10. 工具配置

### 10.1 推荐工具配置
- **ESLint**: JavaScript/TypeScript 代码检查
- **Prettier**: 代码格式化
- **Black**: Python 代码格式化
- **Checkstyle**: Java 代码检查
- **Husky**: Git hooks
- **lint-staged**: 暂存文件检查
- **Commitlint**: 提交信息检查

### 10.2 CI/CD 配置
```
.github/
├── workflows/
│   ├── ci.yml             # 持续集成
│   ├── cd.yml             # 持续部署
│   └── pr-check.yml       # PR 检查
├── CODEOWNERS             # 代码所有者
└── PULL_REQUEST_TEMPLATE.md
```
