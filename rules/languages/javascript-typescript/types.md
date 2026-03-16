## 类型定义规范（节 5）

本文件对应原始规范 `javascript-typescript-standards.md` 中的：

- `## 5. 类型定义`

目前完整内容仍在大文件中，你可以按需将该小节剪切/移动到这里。

推荐迁移步骤：

1. 将 `## 5. 类型定义` 开头到 `## 6. 函数` 之前的所有内容复制到本文件。
2. 在大文件中用简短摘要替换原小节正文，并加一句：
   - “详细规则见 `rules/languages/javascript-typescript/types.md`”。


### 对象联合类型必须拆分为独立类型

- **规则**：当联合类型（`|`）的成员是**对象结构**时，必须为每个成员对象单独定义 `type` / `interface`（可导出），再用它们组合成联合类型；禁止直接内联对象并用 `|` 拼接。
- **原因**：联合类型里的某个成员对象往往会在其他地方被单独复用；提前命名可提升复用性、可读性与可维护性，并避免后续“抽取类型”时的连锁改动。

```ts
// ❌ 禁止：内联对象联合类型（成员对象无法复用、难以引用）
type Result =
  | { ok: true; data: { id: string } }
  | { ok: false; error: { code: string; message: string } };

// ✅ 推荐：拆分命名后再组合联合类型（可单独复用每个成员）
export type OkResult = { ok: true; data: { id: string } };
export type ErrResult = { ok: false; error: { code: string; message: string } };
export type Result2 = OkResult | ErrResult;
```

### 布尔与类型转换规范

- **禁止使用构造函数进行布尔转换**
  - **禁止写法**：
    - `Boolean(result.data?.deleted)`
    - `Boolean(x)` / `Boolean(obj.xxx)`
  - **推荐写法**：
    - 已知是布尔类型：
      - `if (result.data?.deleted) { ... }`
    - 仅做“有无值”判断：
      - `const isDeleted = !!result.data?.deleted`
    - 需要明确比较时：
      - `result.data?.deleted === true`
      - `result.data?.deleted === 1`

- **禁止将构造函数当作通用类型转换函数**
  - **禁止写法（包括但不限于）**：
    - `Boolean(x)` / `Number(x)` / `String(x)`
    - `Object(x)` / `Array(x)` / `Date(x)` 等
  - **推荐写法**：
    - 布尔转换：
      - `!!x`，或显式比较：`x === true` / `x === 1`
    - 数字转换：
      - `parseInt(x, 10)` / `parseFloat(x)`，并对非法输入做校验与兜底
    - 字符串转换：
      - 模板字符串：`` `${x}` ``
      - 注意 `null` / `undefined`：`x ?? ''`
  - **统一约定**：
    - 禁止随意使用 `Boolean/Number/String/Object/Array/Date` 作为“类型转换工具函数”。
    - 需要类型转换时，必须使用语义清晰、行为可预期的方式，并显式处理边界值。