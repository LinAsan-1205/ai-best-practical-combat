# Java 代码规范

## 1. 基础规范

### 1.1 源文件基础
- 使用 UTF-8 编码
- 使用 Unix 风格的换行符 (LF)
- 每个文件只包含一个顶级类
- 文件长度不超过 2000 行

### 1.2 命名规范
| 类型 | 风格 | 示例 |
|------|------|------|
| 包 | 全小写 | `com.example.project` |
| 类/接口 | 大驼峰 | `UserService`, `OrderRepository` |
| 接口 | 大驼峰（形容词/名词） | `Serializable`, `UserMapper` |
| 方法 | 小驼峰 | `getUserById()`, `calculateTotal()` |
| 变量 | 小驼峰 | `userName`, `totalCount` |
| 常量 | 全大写下划线 | `MAX_SIZE`, `DEFAULT_TIMEOUT` |
| 泛型参数 | 单个大写字母 | `T`, `E`, `K`, `V` |

## 2. 代码格式

### 2.1 缩进与空格
- 使用 4 个空格缩进（不使用 Tab）
- 运算符前后加空格
- 逗号后加空格
- 冒号后加空格

```java
// 好的示例
int result = a + b;
List<String> items = Arrays.asList("a", "b", "c");

// 避免
int result=a+b;
```

### 2.2 大括号
- 左大括号不换行（K&R 风格）
- 右大括号单独一行
- 即使只有一行代码也使用大括号

```java
// 好的示例
if (condition) {
    doSomething();
}

// 避免
if (condition)
    doSomething();

// 避免
if (condition) doSomething();
```

### 2.3 每行长度
- 每行不超过 120 个字符
- 长表达式在运算符前换行
- 链式调用每个方法一行

```java
// 好的示例
String result = someVeryLongVariableName
    .doSomething()
    .doAnotherThing()
    .getResult();

// 长参数换行
public void process(
    String firstName,
    String lastName,
    int age,
    String email
) {
    // ...
}
```

## 3. 包声明与导入

### 3.1 包声明
```java
// 包声明在文件最顶部
package com.example.project.service;

// 空行
import java.util.List;
```

### 3.2 导入顺序
```java
// 1. java.* 导入
import java.util.List;
import java.util.Map;

// 2. javax.* 导入
import javax.persistence.Entity;

// 3. 第三方库导入
import org.springframework.stereotype.Service;
import com.google.common.collect.ImmutableList;

// 4. 项目内部导入
import com.example.project.model.User;
import com.example.project.repository.UserRepository;
```

### 3.3 导入规则
```java
// 使用具体导入，不使用通配符
import java.util.List;
import java.util.ArrayList;

// 避免
import java.util.*;

// 静态导入用于常量和工具方法
import static java.util.Collections.emptyList;
import static org.junit.Assert.assertEquals;
```

## 4. 类定义

### 4.1 类结构
```java
package com.example.project.service;

import java.util.List;
import java.util.Objects;

/**
 * 用户服务类，提供用户相关的业务逻辑。
 *
 * @author Your Name
 * @since 1.0.0
 */
public class UserService {
    
    // 1. 静态常量
    private static final int MAX_RETRY = 3;
    private static final String DEFAULT_ROLE = "USER";
    
    // 2. 实例常量
    private final UserRepository userRepository;
    private final EmailService emailService;
    
    // 3. 实例变量
    private int retryCount = 0;
    
    // 4. 构造函数
    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = Objects.requireNonNull(userRepository);
        this.emailService = Objects.requireNonNull(emailService);
    }
    
    // 5. 公共方法
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
    }
    
    // 6. 私有方法
    private void validateUser(User user) {
        if (user == null) {
            throw new IllegalArgumentException("User cannot be null");
        }
    }
}
```

### 4.2 类设计原则
```java
// 不可变类
public final class ImmutableUser {
    private final String id;
    private final String name;
    private final List<String> roles;
    
    public ImmutableUser(String id, String name, List<String> roles) {
        this.id = Objects.requireNonNull(id);
        this.name = Objects.requireNonNull(name);
        // 防御性拷贝
        this.roles = List.copyOf(roles);
    }
    
    // 只有 getter，没有 setter
    public String getId() {
        return id;
    }
    
    public List<String> getRoles() {
        // 返回不可变视图
        return roles;
    }
}

// 工具类
public final class StringUtils {
    // 私有构造函数防止实例化
    private StringUtils() {
        throw new AssertionError("Utility class should not be instantiated");
    }
    
    public static boolean isEmpty(String str) {
        return str == null || str.isEmpty();
    }
}
```

## 5. 方法定义

### 5.1 方法签名
```java
/**
 * 根据用户ID获取用户信息。
 *
 * @param id 用户ID，不能为null
 * @return 用户对象
 * @throws UserNotFoundException 当用户不存在时
 * @throws IllegalArgumentException 当id为null时
 */
public User getUserById(@NonNull Long id) {
    // 方法实现
}

// 参数过多的方法使用 Builder 模式或参数对象
public User createUser(CreateUserRequest request) {
    // ...
}

// 避免
public User createUser(
    String firstName,
    String lastName,
    String email,
    String phone,
    String address,
    int age
) {
    // ...
}
```

### 5.2 方法重载
```java
public class FileProcessor {
    
    // 重载方法应该做相同的事情
    public void process(File file) {
        process(file, StandardCharsets.UTF_8);
    }
    
    public void process(File file, Charset charset) {
        // 实际处理逻辑
    }
}
```

## 6. 变量与常量

### 6.1 变量声明
```java
// 每个变量单独声明
String firstName = "John";
String lastName = "Doe";

// 避免
String firstName, lastName;

// 局部变量尽量延迟初始化
String result;
if (condition) {
    result = "A";
} else {
    result = "B";
}

// 使用增强 for 循环
for (User user : users) {
    process(user);
}

// 避免使用传统 for 循环遍历集合
// 避免：for (int i = 0; i < users.size(); i++)
```

### 6.2 常量定义
```java
public class Config {
    // 常量使用全大写下划线命名
    public static final int MAX_CONNECTIONS = 100;
    public static final String DEFAULT_ENCODING = "UTF-8";
    
    // 枚举用于相关常量
    public enum Status {
        PENDING,
        ACTIVE,
        INACTIVE
    }
}
```

### 6.3 硬编码规范

硬编码（Magic Values）需要**根据场景判断**是否提取为常量。

#### 必须使用常量的场景

| 场景 | 说明 | 示例 |
|------|------|------|
| **业务状态值** | 表示业务状态的字符串/数字 | 用户状态、订单状态、权限标识 |
| **配置参数** | 可调整的参数值 | 超时时间、重试次数、分页大小 |
| **外部接口** | 与外部系统交互的标识 | API 地址、错误码、响应消息 |
| **多处使用** | 在多个地方重复使用的值 | 默认值、边界值、魔法数字 |
| **可能变化** | 未来可能需要修改的值 | 版本号、限制值、阈值 |

```java
// ❌ 避免 - 业务状态值硬编码
if (status.equals("active")) {}
if (order.getStatus() == 2) {}  // 2 代表什么？

// ❌ 避免 - 配置参数硬编码
int timeout = 5000;  // 5秒？为什么是这个值？
int maxRetries = 3;  // 3次？可以调整吗？

// ✅ 好的示例 - 使用常量
public class UserConstants {
    public static final String STATUS_ACTIVE = "active";
    public static final String STATUS_INACTIVE = "inactive";
}

public class AppConfig {
    public static final int REQUEST_TIMEOUT_MS = 5000;  // 请求超时时间，单位毫秒
    public static final int MAX_RETRY_ATTEMPTS = 3;     // 最大重试次数
}

if (status.equals(UserConstants.STATUS_ACTIVE)) {}
int timeout = AppConfig.REQUEST_TIMEOUT_MS;
```

#### 可以直接使用字面量的场景

| 场景 | 说明 | 示例 |
|------|------|------|
| **语义明确** | 值本身就能说明含义 | `count > 0`, `index + 1` |
| **局部使用** | 只在当前上下文使用 | 循环计数器、临时计算 |
| **标准值** | 行业/语言标准值 | `Math.PI`, `100` (百分比) |
| **测试数据** | 测试用例中的数据 | mock 数据、测试输入 |

```java
// ✅ 可以直接使用 - 语义明确
if (count > 0) {}
int nextIndex = currentIndex + 1;
double percentage = (value / total) * 100;

// ✅ 可以直接使用 - 局部使用
for (int i = 0; i < items.size(); i++) {}
String firstItem = items.get(0);
String lastItem = items.get(items.size() - 1);

// ✅ 可以直接使用 - 标准值
double circumference = 2 * Math.PI * radius;
boolean isEmpty = str.length() == 0;

// ✅ 可以直接使用 - 测试数据
@Test
void shouldAddNumbers() {
    assertEquals(5, calculator.add(2, 3));  // 测试数据直接使用
}
```

#### 需要注释的场景

当使用字面量时，如果含义不够直观，应添加注释说明：

```java
// ✅ 添加注释说明
int maxRetries = 3; // 网络请求失败后的最大重试次数
long debounceDelay = 300; // 输入防抖延迟，单位毫秒

// ✅ 复杂计算添加注释
long cacheDuration = 60 * 60 * 1000; // 1小时的毫秒数

// ❌ 避免 - 魔法数字无注释
double result = value * 0.15;  // 0.15 是什么？

// ✅ 好的示例 - 添加注释或使用常量
double result = value * 0.15; // 15% 的服务费
// 或
private static final double SERVICE_FEE_RATE = 0.15;
double result = value * SERVICE_FEE_RATE;
```

## 7. 控制流

### 7.1 条件语句
```java
// 好的示例
if (user == null) {
    throw new IllegalArgumentException("User cannot be null");
}

// 避免深层嵌套
// 避免：
if (user != null) {
    if (user.isActive()) {
        if (user.hasPermission("admin")) {
            // ...
        }
    }
}

// 好的示例：提前返回
if (user == null) {
    return;
}
if (!user.isActive()) {
    return;
}
if (!user.hasPermission("admin")) {
    return;
}
// 处理逻辑
```

### 7.2 Switch 语句
```java
// Java 12+ 使用 switch 表达式
public String getStatusText(Status status) {
    return switch (status) {
        case PENDING -> "待处理";
        case ACTIVE -> "活跃";
        case INACTIVE -> "非活跃";
        default -> throw new IllegalArgumentException("Unknown status: " + status);
    };
}

// 传统 switch 语句
public void processStatus(Status status) {
    switch (status) {
        case PENDING:
            processPending();
            break;
        case ACTIVE:
            processActive();
            break;
        default:
            throw new IllegalArgumentException("Unknown status: " + status);
    }
}
```

### 7.3 异常处理
```java
// 好的示例
try {
    processFile(file);
} catch (FileNotFoundException e) {
    logger.error("File not found: {}", file.getName(), e);
    throw new BusinessException("文件不存在", e);
} catch (IOException e) {
    logger.error("IO error processing file: {}", file.getName(), e);
    throw new BusinessException("文件处理失败", e);
}

// 使用 try-with-resources
try (InputStream is = new FileInputStream(file);
     BufferedReader reader = new BufferedReader(new InputStreamReader(is))) {
    return reader.readLine();
}

// 自定义异常
public class BusinessException extends RuntimeException {
    private final String errorCode;
    
    public BusinessException(String message) {
        super(message);
        this.errorCode = "GENERAL_ERROR";
    }
    
    public BusinessException(String message, Throwable cause) {
        super(message, cause);
        this.errorCode = "GENERAL_ERROR";
    }
}
```

## 8. 集合处理

### 8.1 集合使用
```java
// 接口类型作为返回类型和参数类型
public List<User> getUsers() {
    return new ArrayList<>();
}

// 使用泛型
List<String> names = new ArrayList<>();
Map<String, User> userMap = new HashMap<>();

// Java 9+ 使用工厂方法
List<String> list = List.of("a", "b", "c");
Map<String, Integer> map = Map.of("a", 1, "b", 2);

// 使用 Stream API
List<String> activeUserNames = users.stream()
    .filter(User::isActive)
    .map(User::getName)
    .collect(Collectors.toList());

// 使用 Optional
public Optional<User> findById(Long id) {
    return Optional.ofNullable(userMap.get(id));
}

// 使用 Optional 避免空指针
String name = findById(1L)
    .map(User::getName)
    .orElse("Unknown");
```

## 9. 注释与文档

### 9.1 Javadoc 规范
```java
/**
 * 用户服务类，提供用户相关的业务逻辑操作。
 *
 * <p>该类封装了用户数据的增删改查操作，并提供了用户认证相关的功能。</p>
 *
 * @author Zhang San
 * @since 1.0.0
 * @see UserRepository
 */
public class UserService {
    
    /**
     * 根据用户ID获取用户信息。
     *
     * <p>如果用户不存在，将抛出 {@link UserNotFoundException}。</p>
     *
     * @param id 用户ID，必须不为null且大于0
     * @return 用户对象，不会返回null
     * @throws UserNotFoundException 当用户不存在时
     * @throws IllegalArgumentException 当id为null或小于等于0时
     * 
     * @since 1.0.0
     */
    public User getUserById(Long id) {
        // ...
    }
}
```

### 9.2 行内注释
```java
// 好的示例：解释"为什么"而非"是什么"
// 由于历史原因，这里需要特殊处理旧版本数据
if (version < 2) {
    migrateOldData();
}

// 避免：显而易见的注释
// 增加计数器
counter++;
```

## 10. 测试规范

### 10.1 JUnit 5 测试
```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import static org.assertj.core.api.Assertions.*;

@DisplayName("用户服务测试")
class UserServiceTest {
    
    private UserService userService;
    private UserRepository userRepository;
    
    @BeforeEach
    void setUp() {
        userRepository = mock(UserRepository.class);
        userService = new UserService(userRepository);
    }
    
    @Test
    @DisplayName("根据ID获取用户 - 用户存在时返回用户")
    void getUserById_WhenUserExists_ReturnsUser() {
        // Given
        Long userId = 1L;
        User expectedUser = new User(userId, "John");
        when(userRepository.findById(userId)).thenReturn(Optional.of(expectedUser));
        
        // When
        User result = userService.getUserById(userId);
        
        // Then
        assertThat(result).isEqualTo(expectedUser);
    }
    
    @Test
    @DisplayName("根据ID获取用户 - 用户不存在时抛出异常")
    void getUserById_WhenUserNotExists_ThrowsException() {
        // Given
        Long userId = 1L;
        when(userRepository.findById(userId)).thenReturn(Optional.empty());
        
        // When & Then
        assertThatThrownBy(() -> userService.getUserById(userId))
            .isInstanceOf(UserNotFoundException.class)
            .hasMessageContaining("User not found");
    }
    
    @ParameterizedTest
    @CsvSource({
        "1, true",
        "0, false",
        "-1, false"
    })
    @DisplayName("验证用户ID")
    void isValidUserId(Long id, boolean expected) {
        assertThat(userService.isValidUserId(id)).isEqualTo(expected);
    }
}
```

## 11. 项目结构

### 11.1 Maven 项目结构
```
myproject/
├── pom.xml
├── README.md
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── myproject/
│   │   │               ├── Application.java
│   │   │               ├── config/
│   │   │               ├── controller/
│   │   │               ├── service/
│   │   │               ├── repository/
│   │   │               ├── model/
│   │   │               ├── dto/
│   │   │               ├── exception/
│   │   │               └── util/
│   │   └── resources/
│   │       ├── application.yml
│   │       └── static/
│   └── test/
│       └── java/
│           └── com/
│               └── example/
│                   └── myproject/
│                       └── service/
│                           └── UserServiceTest.java
```

## 12. 工具配置

### 12.1 推荐工具
- **IntelliJ IDEA**: IDE
- **Maven/Gradle**: 构建工具
- **Checkstyle**: 代码风格检查
- **SpotBugs**: 静态分析
- **JaCoCo**: 代码覆盖率
- **JUnit 5**: 测试框架
- **AssertJ**: 断言库
- **Mockito**: 模拟框架

### 12.2 Checkstyle 配置
```xml
<?xml version="1.0"?>
<!DOCTYPE module PUBLIC
    "-//Checkstyle//DTD Checkstyle Configuration 1.3//EN"
    "https://checkstyle.org/dtds/configuration_1_3.dtd">
<module name="Checker">
    <module name="TreeWalker">
        <module name="Indentation">
            <property name="basicOffset" value="4"/>
            <property name="tabWidth" value="4"/>
        </module>
        <module name="LineLength">
            <property name="max" value="120"/>
        </module>
        <module name="MethodLength">
            <property name="max" value="100"/>
        </module>
        <module name="ParameterNumber">
            <property name="max" value="5"/>
        </module>
    </module>
</module>
```
