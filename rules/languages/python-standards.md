# Python 代码规范

## 1. 基础规范

### 1.1 遵循 PEP 8
- 使用 4 个空格缩进
- 每行不超过 79 个字符（文档字符串/注释不超过 72 个）
- 使用 UTF-8 编码
- 文件末尾保留一个空行

### 1.2 命名规范
| 类型 | 风格 | 示例 |
|------|------|------|
| 模块 | 小写下划线 | `my_module.py` |
| 包 | 小写 | `mypackage` |
| 类 | 大驼峰 | `MyClass` |
| 函数/方法 | 小写下划线 | `my_function()` |
| 常量 | 全大写下划线 | `MAX_COUNT` |
| 变量 | 小写下划线 | `my_variable` |
| 私有属性 | 前导下划线 | `_private_var` |
| 强私有 | 双前导下划线 | `__very_private` |
| 魔术方法 | 双下划线包围 | `__init__`, `__str__` |

## 2. 代码布局

### 2.1 导入顺序
```python
# 1. 标准库导入
import os
import sys
from datetime import datetime

# 2. 第三方库导入
import requests
from flask import Flask

# 3. 本地应用/库导入
from myapp.models import User
from myapp.utils import helper
```

### 2.2 导入风格
```python
# 好的示例
from subprocess import Popen, PIPE
import os

# 避免
from subprocess import *  # 不要使用通配符导入
```

### 2.3 空行使用
```python
# 类定义前两个空行
class MyClass:
    
    # 方法定义间一个空行
    def method_one(self):
        pass
    
    def method_two(self):
        pass


# 类之间两个空行
class AnotherClass:
    pass
```

## 3. 字符串处理

### 3.1 引号使用
```python
# 优先使用单引号，但保持一致
name = 'John'
message = "It's a nice day"

# 多行字符串使用三引号
description = '''
This is a multi-line
string description.
'''
```

### 3.2 字符串格式化
```python
# Python 3.6+ 使用 f-string（推荐）
name = 'John'
age = 30
message = f'Hello, {name}! You are {age} years old.'

# 复杂格式化
value = 3.14159
formatted = f'Value: {value:.2f}'  # Value: 3.14

# 避免使用 % 格式化
# 避免：'Hello, %s' % name

# 旧式 .format() 方法
# 仅在需要时用于兼容性
template = 'Hello, {}!'.format(name)
```

## 4. 函数定义

### 4.1 函数设计
```python
def calculate_total(
    items: list[dict],
    tax_rate: float = 0.08,
    *,
    discount: float = 0.0
) -> float:
    """Calculate the total price of items.
    
    Args:
        items: List of item dictionaries with 'price' and 'quantity' keys.
        tax_rate: Tax rate as a decimal (default: 0.08).
        discount: Discount rate as a decimal (keyword-only, default: 0.0).
    
    Returns:
        The total price including tax and discount.
    
    Raises:
        ValueError: If items list is empty.
    """
    if not items:
        raise ValueError('Items list cannot be empty')
    
    subtotal = sum(item['price'] * item['quantity'] for item in items)
    discount_amount = subtotal * discount
    taxable = subtotal - discount_amount
    tax = taxable * tax_rate
    
    return taxable + tax
```

### 4.2 类型注解
```python
from typing import Optional, Union, List, Dict, Any

def process_data(
    data: Dict[str, Any],
    options: Optional[Dict[str, bool]] = None
) -> Union[str, None]:
    """Process data with optional configuration."""
    if options is None:
        options = {}
    
    # 处理逻辑
    return result

# Python 3.9+ 可以使用内置类型
from __future__ import annotations

def modern_types(
    items: list[str],
    mapping: dict[str, int]
) -> tuple[str, int]:
    pass
```

## 5. 类定义

### 5.1 类设计
```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class User:
    """Represents a user in the system."""
    
    id: int
    name: str
    email: str
    is_active: bool = True
    _password: Optional[str] = None
    
    def __post_init__(self):
        """Validate after initialization."""
        if not self.name:
            raise ValueError('Name cannot be empty')
    
    def deactivate(self) -> None:
        """Deactivate the user account."""
        self.is_active = False
    
    def __str__(self) -> str:
        return f'User({self.name}, {self.email})'


# 继承示例
class AdminUser(User):
    """Admin user with additional privileges."""
    
    def __init__(self, *args, permissions: list[str] = None, **kwargs):
        super().__init__(*args, **kwargs)
        self.permissions = permissions or []
    
    def has_permission(self, permission: str) -> bool:
        return permission in self.permissions
```

### 5.2 属性装饰器
```python
class Circle:
    def __init__(self, radius: float):
        self._radius = radius
    
    @property
    def radius(self) -> float:
        """Get the radius."""
        return self._radius
    
    @radius.setter
    def radius(self, value: float):
        """Set the radius with validation."""
        if value < 0:
            raise ValueError('Radius cannot be negative')
        self._radius = value
    
    @property
    def area(self) -> float:
        """Calculate the area."""
        import math
        return math.pi * self._radius ** 2
```

## 6. 异常处理

### 6.1 异常使用
```python
# 好的示例
def read_file(path: str) -> str:
    try:
        with open(path, 'r', encoding='utf-8') as f:
            return f.read()
    except FileNotFoundError:
        logger.error(f'File not found: {path}')
        raise
    except PermissionError:
        logger.error(f'Permission denied: {path}')
        raise
    except Exception as e:
        logger.error(f'Unexpected error reading {path}: {e}')
        raise

# 自定义异常
class ValidationError(Exception):
    """Raised when validation fails."""
    pass

class NotFoundError(Exception):
    """Raised when a resource is not found."""
    pass
```

### 6.2 上下文管理器
```python
# 使用 with 语句
with open('file.txt', 'r') as f:
    content = f.read()

# 自定义上下文管理器
from contextlib import contextmanager

@contextmanager
def managed_resource(name: str):
    resource = acquire_resource(name)
    try:
        yield resource
    finally:
        release_resource(resource)

# 使用
with managed_resource('my_resource') as r:
    process(r)
```

## 7. 列表/字典推导式

### 7.1 推导式使用
```python
# 列表推导式
squares = [x**2 for x in range(10)]
even_squares = [x**2 for x in range(10) if x % 2 == 0]

# 字典推导式
name_lengths = {name: len(name) for name in names}

# 集合推导式
unique_lengths = {len(name) for name in names}

# 生成器表达式（惰性求值）
total = sum(x**2 for x in range(1000000))

# 避免过于复杂的推导式
# 避免：
result = [f(x) for x in items if g(x) if h(x) for y in x if k(y)]

# 好的示例：拆分为多个步骤
filtered = [x for x in items if g(x) and h(x)]
result = [f(x) for x in filtered]
```

## 8. 文档字符串

### 8.1 文档字符串格式
```python
def complex_function(param1: int, param2: str) -> bool:
    """Short description of the function.
    
    Longer description explaining the function's behavior,
    any important details, and usage examples.
    
    Args:
        param1: Description of param1.
        param2: Description of param2.
    
    Returns:
        Description of return value.
    
    Raises:
        ValueError: When param1 is negative.
        TypeError: When param2 is not a string.
    
    Example:
        >>> complex_function(1, 'test')
        True
        >>> complex_function(-1, 'test')
        Traceback (most recent call last):
            ...
        ValueError: param1 must be non-negative
    """
    pass


class ExampleClass:
    """Summary of the class.
    
    Longer description of the class.
    
    Attributes:
        attr1: Description of attr1.
        attr2: Description of attr2.
    """
    
    def method(self):
        """Class method docstring."""
        pass
```

## 9. 测试规范

### 9.1 使用 pytest
```python
# test_calculator.py
import pytest
from calculator import add, divide

class TestCalculator:
    """Test cases for calculator functions."""
    
    def test_add_positive_numbers(self):
        """Test adding two positive numbers."""
        assert add(2, 3) == 5
    
    def test_add_negative_numbers(self):
        """Test adding negative numbers."""
        assert add(-1, -1) == -2
    
    def test_divide_by_zero(self):
        """Test division by zero raises exception."""
        with pytest.raises(ZeroDivisionError):
            divide(1, 0)
    
    @pytest.mark.parametrize('a,b,expected', [
        (1, 1, 2),
        (0, 0, 0),
        (-1, 1, 0),
    ])
    def test_add_parametrized(self, a, b, expected):
        """Test add with multiple inputs."""
        assert add(a, b) == expected
```

## 10. 项目结构

### 10.1 推荐目录结构
```
myproject/
├── README.md
├── requirements.txt
├── setup.py
├── pyproject.toml
├── .gitignore
├── .flake8
├── pytest.ini
├── src/
│   └── myproject/
│       ├── __init__.py
│       ├── models/
│       │   ├── __init__.py
│       │   └── user.py
│       ├── services/
│       │   ├── __init__.py
│       │   └── user_service.py
│       └── utils/
│           ├── __init__.py
│           └── helpers.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py
│   ├── unit/
│   │   └── test_user.py
│   └── integration/
│       └── test_api.py
└── docs/
    └── api.md
```

## 11. 工具配置

### 11.1 推荐工具
- **black**: 代码格式化
- **isort**: 导入排序
- **flake8**: 代码风格检查
- **mypy**: 类型检查
- **pylint**: 代码质量检查
- **pytest**: 测试框架

### 11.2 pyproject.toml 示例
```toml
[tool.black]
line-length = 88
target-version = ['py39']

[tool.isort]
profile = "black"
multi_line_output = 3

[tool.mypy]
python_version = "3.9"
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_classes = "Test*"
python_functions = "test_*"
```
