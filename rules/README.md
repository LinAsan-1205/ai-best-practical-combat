# 代码规范文档

本目录包含个人开发使用的代码规范文档，按类别组织，便于按需引用。

## 目录结构

```
rules/
├── README.md                 # 本文件
├── core/                     # 核心规范（通用）
│   ├── general-coding-standards.md    # 通用代码规范
│   └── quick-reference.md             # 快速参考（精简版）
├── languages/                # 语言特定规范
│   ├── javascript-typescript-standards.md
│   ├── java-standards.md
│   └── python-standards.md
└── practices/                # 开发实践规范
    ├── git-commit-standards.md
    └── project-structure-standards.md
```

## 使用建议

### 快速开始
参考 [`core/quick-reference.md`](./core/quick-reference.md) 获取最常用的规范要点。

### 按场景选择

| 场景 | 参考文档 |
|------|----------|
| 新项目搭建 | [`practices/project-structure-standards.md`](./practices/project-structure-standards.md) |
| 日常编码 | [`core/general-coding-standards.md`](./core/general-coding-standards.md) + 对应语言规范 |
| 代码提交 | [`practices/git-commit-standards.md`](./practices/git-commit-standards.md) |
| JS/TS 项目 | [`languages/javascript-typescript-standards.md`](./languages/javascript-typescript-standards.md) |
| Java 项目 | [`languages/java-standards.md`](./languages/java-standards.md) |
| Python 项目 | [`languages/python-standards.md`](./languages/python-standards.md) |

## 文件大小参考

| 文件 | 大小 | 说明 |
|------|------|------|
| `core/quick-reference.md` | ~3KB | 精简版，推荐日常使用 |
| `core/general-coding-standards.md` | ~3KB | 通用规范 |
| `languages/javascript-typescript-standards.md` | ~48KB | 最详细，按需引用 |
| `languages/java-standards.md` | ~13KB | Java 专用 |
| `languages/python-standards.md` | ~9KB | Python 专用 |
| `practices/git-commit-standards.md` | ~5KB | Git 提交规范 |
| `practices/project-structure-standards.md` | ~14KB | 项目结构指南 |

## 规范更新

这些规范会根据实际开发经验和技术发展持续更新。建议：
- 定期回顾和更新规范
- 根据团队实际情况调整
- 保持规范的一致性

---

*最后更新：2026-03-12*
