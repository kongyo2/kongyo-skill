---
name: typescript
description: "TypeScript project development skill. Use when creating TypeScript files, Node.js projects, or when working with .ts files. Covers code design principles (separation of concerns, state/logic separation, readability), file naming conventions (lowerCamelCase), coding rules (function-based architecture, no classes, .ts import extensions, node: namespace for stdlib), and linting requirements."
---

# コード設計
- 関心の分離を保つ
- 状態とロジックを分離する
- 可読性と保守性を重視する
- コントラクト層（API/型）を厳密に定義し、実装層は再生成可能に保つ

# ツール
- Node.js: npm, v24+

# Coding Rules
- File naming convention: `src/<lowerCamelCase>.ts`
- Use functions and function scope instead of classes
- Add `.ts` extension to imports for deno compatibility
- Do not disable any lint rules without explicit user approval
- Export a function that matches the filename, keep everything else private
- All lint errors must be fixed before committing code
- .oxlintrc.json must not be modified without user permission
- When importing Node.js standard library modules, use the `node:` namespace prefix (e.g., `import path from "node:path"`, `import fs from "node:fs"`)
