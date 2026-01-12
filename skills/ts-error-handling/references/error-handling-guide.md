# neverthrow Setup Guide

This project uses the neverthrow library for Result type-based error handling instead of throwing exceptions.

## Installation

```bash
pnpm add neverthrow
```

## Agent Prompt Configuration

When working with this project, agents should be instructed to use neverthrow for error handling. Add the following content to your agent prompt configuration (e.g., `CLAUDE.md`):

### Required Agent Instructions

```markdown
## Error Handling Policy

This project follows a strict no-exceptions design policy using neverthrow:

- **NEVER throw exceptions** in application code
- **ALWAYS use Result types** for error handling instead of throwing
- **ALL functions that can fail** must return `Result<T, E>` instead of throwing
- Use explicit error handling over implicit exception propagation

### neverthrow Usage

- Install: `pnpm add neverthrow`
- Import: `import { Result, ok, err, fromThrowable } from "neverthrow"`
- For async: `import { ResultAsync } from "neverthrow"`
- Use `result.isOk()` and `result.isErr()` for type checking

### Mandatory Practices

1. **Function Return Types**: All functions that can fail must return `Result<SuccessType, ErrorType>`
2. **Error Checking**: Always use `result.isOk()` / `result.isErr()` for type-safe error checking
3. **No Exception Throwing**: Never use `throw` statements in application code
4. **Async Operations**: Use `ResultAsync.fromPromise()` for wrapping promises
5. **External Libraries**: Wrap third-party code that might throw using `fromThrowable()`
```

## Basic Usage Examples

### Synchronous Functions

```typescript
import { Result, ok, err } from "neverthrow"

function divide(a: number, b: number): Result<number, string> {
  if (b === 0) {
    return err("Division by zero")
  }
  return ok(a / b)
}

const result = divide(10, 2)
if (result.isOk()) {
  console.log(result.value)
} else {
  console.error(result.error)
}
```

### Wrapping Throwing Functions

```typescript
import { fromThrowable } from "neverthrow"

const safeJsonParse = fromThrowable(
  JSON.parse,
  (error) => `Failed to parse JSON: ${error}`
)

const result = safeJsonParse('{"valid": true}')
```

### Async Operations

```typescript
import { ResultAsync } from "neverthrow"

function fetchUser(id: string): ResultAsync<User, string> {
  return ResultAsync.fromPromise(
    fetch(`/api/users/${id}`).then(res => res.json()),
    () => "Failed to fetch user"
  )
}
```

### Chaining Operations

```typescript
import { Result, ok, err } from "neverthrow"

const result = parseAge("25")
  .map(age => age * 2)
  .mapErr(error => `Validation error: ${error}`)
  .andThen(doubledAge => validateRange(doubledAge))
```

## Agent Prompt Template

Copy and paste this template into your agent configuration:

```markdown
## Error Handling Requirements

- Do not throw exceptions in application code
- Use neverthrow Result types for all error handling
- Return `Result<T, E>` from functions that can fail
- Use `result.isOk()` and `result.isErr()` for type-safe error checking
- Import from neverthrow: `import { Result, ok, err, fromThrowable, ResultAsync } from "neverthrow"`
- Wrap external throwing code with `fromThrowable()`
- Wrap promises with `ResultAsync.fromPromise()`
```

## Quick Reference

| Function | Description |
|----------|-------------|
| `ok(value)` | Create success Result |
| `err(error)` | Create failure Result |
| `result.isOk()` | Type guard for success |
| `result.isErr()` | Type guard for failure |
| `result.map(fn)` | Transform success value |
| `result.mapErr(fn)` | Transform error value |
| `result.andThen(fn)` | Chain Result-returning operations |
| `Result.combine([...])` | Combine multiple Results |
| `fromThrowable(fn, errorHandler)` | Wrap throwing function |
| `ResultAsync.fromPromise(promise, errorHandler)` | Wrap Promise |

## References

- neverthrow library: https://github.com/supermacro/neverthrow
- Original guide: https://raw.githubusercontent.com/mizchi/ts-guide/refs/heads/main/docs/ts-guide/error-handling.md
