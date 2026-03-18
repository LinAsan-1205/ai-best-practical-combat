---
name: rules-navigator
description: Navigate and apply the repository rules under rules/ (coding standards, language guides, git commit standards, project structure). Use when the user asks to follow team/project conventions, mentions "规则/规范/标准", requests code review against standards, or asks how to structure code/commits/projects in this repo.
---

# Rules Navigator（项目规范导航）

本技能的目标是：**把仓库里的 `rules/` 目录当作“单一事实来源”**，在编码、审查、重构、提交信息、项目结构等场景中，快速定位并执行对应规范。

## 快速开始（默认路径）

当需要“按规范做事”时，按这个顺序做：

1. 先读精简版：`rules/core/quick-reference.md`
2. 再按场景补齐：
   - 日常编码通用：`rules/core/general-coding-standards.md`
   - 语言规范：`rules/languages/<language>-standards.md` 或 `rules/languages/javascript-typescript-standards.md`
   - Git 提交：`rules/practices/git-commit-standards.md`
   - 项目结构：`rules/practices/project-structure-standards.md`

## 适用场景与工作流

### A. 写代码 / 改代码（实现类任务）

- **先判定语言与栈**：JS/TS、Python、Java（若不确定，以仓库现状/文件扩展名为准）
- **读取最低必需规则集**：
  - 总是：`rules/core/quick-reference.md`
  - 再加：对应语言标准（见下方“语言路由”）
- **执行规则**（关键点）：
  - 命名、格式、组织结构、错误处理、时间戳、硬编码等以规则为准
  - 若规则之间出现冲突：以 `rules/core/*` 与“该语言标准”中更具体/更严格的条目为准

### B. 代码审查 / 规范检查（review 类任务）

- **先读**：`rules/core/quick-reference.md`
- **再读**：对应语言标准中的“组织/命名/注释/类型/性能”等章节
- **输出审查反馈时**：
  - 明确标注“违反了哪条规则”，并给出“符合规范的替代写法/结构”
  - 优先给**最小改动**的修复建议，除非规范要求结构性调整

### C. 写提交信息 / 规范化提交（git 类任务）

- **读取并遵循**：`rules/practices/git-commit-standards.md`
- **输出时**：严格按文档要求的 `<type>(<scope>): <subject>` 结构（如适用）

### D. 新项目搭建 / 目录规划（结构类任务）

- **读取并遵循**：`rules/practices/project-structure-standards.md`
- 需要语言/框架细节时，再补读对应语言标准

## 语言路由（选读）

- **JavaScript/TypeScript**：`rules/languages/javascript-typescript-standards.md`
  - 若目标更细：优先读取 `rules/languages/javascript-typescript/` 下的对应主题文档
- **Python**：`rules/languages/python-standards.md`
- **Java**：`rules/languages/java-standards.md`

## 规范引用约定（对外输出时）

当需要把规范“写进结论/建议”里（例如 review 建议、PR 说明、技术方案）：

- 只引用必要条目，避免整段粘贴
- 引用时用相对路径指向来源，例如：`rules/core/quick-reference.md`

## 资源入口

- 目录索引：`rules/README.md`
- 精简速查：`rules/core/quick-reference.md`
- 通用详版：`rules/core/general-coding-standards.md`
- JS/TS：`rules/languages/javascript-typescript-standards.md`
- Python：`rules/languages/python-standards.md`
- Java：`rules/languages/java-standards.md`
- Git：`rules/practices/git-commit-standards.md`
- 项目结构：`rules/practices/project-structure-standards.md`

