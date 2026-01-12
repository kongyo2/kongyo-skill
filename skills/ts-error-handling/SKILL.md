---
name: ts-error-handling
description: Apply Result type-based error handling to TypeScript code using neverthrow library. Use this skill when implementing error handling, refactoring exception-based code to Result types, or when working on projects that mandate no-throw policies. Converts try-catch blocks, thrown errors, and Promise rejections into explicit Result types for type-safe error management.
---

# TypeScript Error Handling with neverthrow

## Overview

This skill implements TypeScript error handling using the neverthrow library's Result types instead of exceptions. Transform exception-based code into explicit, type-safe error handling that makes failure paths visible and forces deliberate error management decisions at each function boundary.

## Setup

Install the neverthrow package:

```bash
pnpm add neverthrow
```

## When to Use This Skill

Use this skill when:
- Implementing error handling in TypeScript projects with no-exceptions policies
- Refactoring try-catch blocks to Result type patterns
- Wrapping external APIs that throw exceptions
- Converting Promise rejections to Result types
- Adding type-safe error handling to existing functions
- Building systems requiring explicit error propagation

## Core Principles

### No-Exceptions Design Policy

**Mandatory Requirements:**
1. **NEVER throw exceptions** in application code
2. **ALWAYS use Result types** for error handling instead of throwing
3. **ALL functions that can fail** must return `Result<T, E>` instead of throwing
4. Use explicit error handling over implicit exception propagation

### Result Type Structure

neverthrow provides the `Result<T, E>` type representing computation outcomes that can either succeed or fail:

```typescript
import { Result, ok, err } from "neverthrow"

// ok(value) creates a success Result
// err(error) creates a failure Result
```

## Basic Usage

### Creating Results

```typescript
import { Result, ok, err } from "neverthrow"

// Return Result from functions that can fail
function divide(a: number, b: number): Result<number, string> {
  if (b === 0) {
    return err("Division by zero")
  }
  return ok(a / b)
}

// Check results with type guards
const result = divide(10, 2)
if (result.isOk()) {
  console.log(result.value) // Type: number
} else {
  console.error(result.error) // Type: string
}
```

### Async Operations with ResultAsync

```typescript
import { ResultAsync, okAsync, errAsync } from "neverthrow"

async function fetchUser(id: string): ResultAsync<User, string> {
  return ResultAsync.fromPromise(
    fetch(`/api/users/${id}`).then(res => res.json()),
    () => "Failed to fetch user"
  )
}

// Usage
const result = await fetchUser("123")
if (result.isOk()) {
  console.log(result.value)
} else {
  console.error(result.error)
}
```

## Common Transformation Patterns

### Pattern 1: Converting Throwing Functions

**Before (throwing exceptions):**
```typescript
function loadUser(id: string): User {
  if (!id) {
    throw new Error("User ID required")
  }
  const user = db.findUser(id)
  if (!user) {
    throw new Error(`User ${id} not found`)
  }
  return user
}
```

**After (Result types):**
```typescript
import { Result, ok, err } from "neverthrow"

function loadUser(id: string): Result<User, string> {
  if (!id) {
    return err("User ID required")
  }
  const user = db.findUser(id)
  if (!user) {
    return err(`User ${id} not found`)
  }
  return ok(user)
}

// Usage
const result = loadUser("123")
if (result.isOk()) {
  processUser(result.value)
} else {
  logger.error(result.error)
}
```

### Pattern 2: Wrapping External Throwing Code with fromThrowable

```typescript
import { Result, fromThrowable } from "neverthrow"

// Wrap synchronous throwing functions
const safeJsonParse = fromThrowable(
  JSON.parse,
  (error) => `Failed to parse JSON: ${error}`
)

function parseConfig(text: string): Result<Config, string> {
  return safeJsonParse(text) as Result<Config, string>
}

// Usage
const result = parseConfig('{"valid": true}')
if (result.isOk()) {
  console.log(result.value)
} else {
  console.error(result.error)
}
```

### Pattern 3: Converting try-catch Blocks

**Before (try-catch):**
```typescript
function readConfig(path: string): Config {
  try {
    const content = fs.readFileSync(path, 'utf-8')
    return JSON.parse(content)
  } catch (error) {
    throw new Error(`Failed to read config: ${error}`)
  }
}
```

**After (Result types with fromThrowable):**
```typescript
import { Result, fromThrowable } from "neverthrow"

const safeReadFile = fromThrowable(
  (path: string) => fs.readFileSync(path, 'utf-8'),
  (error) => `Failed to read file: ${error}`
)

const safeParseJSON = fromThrowable(
  (text: string) => JSON.parse(text),
  (error) => `Failed to parse JSON: ${error}`
)

function readConfig(path: string): Result<Config, string> {
  return safeReadFile(path)
    .andThen(content => safeParseJSON(content) as Result<Config, string>)
}
```

### Pattern 4: Converting Async/Promise Code

**Before (Promise with rejection):**
```typescript
async function fetchData(url: string): Promise<Data> {
  const response = await fetch(url)
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`)
  }
  return response.json()
}
```

**After (ResultAsync):**
```typescript
import { ResultAsync } from "neverthrow"

function fetchData(url: string): ResultAsync<Data, string> {
  return ResultAsync.fromPromise(
    fetch(url).then(async response => {
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`)
      }
      return response.json()
    }),
    (error) => error instanceof Error ? error.message : String(error)
  )
}

// Usage
const result = await fetchData("https://api.example.com/data")
if (result.isOk()) {
  processData(result.value)
} else {
  logger.error("Fetch failed:", result.error)
}
```

### Pattern 5: Chaining Result Operations

**Chaining with map and andThen:**

```typescript
import { Result, ok, err } from "neverthrow"

function parseAge(input: string): Result<number, string> {
  const num = parseInt(input, 10)
  if (isNaN(num)) {
    return err("Invalid number")
  }
  if (num < 0 || num > 150) {
    return err("Age out of range")
  }
  return ok(num)
}

function formatAge(age: number): string {
  return `${age} years old`
}

// Chain operations
const result = parseAge("25")
  .map(formatAge) // Only runs if parseAge succeeded
  .mapErr(error => `Validation error: ${error}`)

if (result.isOk()) {
  console.log(result.value) // "25 years old"
} else {
  console.error(result.error) // "Validation error: ..."
}
```

### Pattern 6: Combining Multiple Results

**Using Result.combine:**

```typescript
import { Result, ok, err } from "neverthrow"

function validateEmail(email: string): Result<string, string> {
  if (!email.includes("@")) {
    return err("Invalid email format")
  }
  return ok(email)
}

function validatePassword(password: string): Result<string, string> {
  if (password.length < 8) {
    return err("Password too short")
  }
  return ok(password)
}

function createAccount(
  email: string,
  password: string
): Result<Account, string> {
  return Result.combine([
    validateEmail(email),
    validatePassword(password)
  ]).map(([validEmail, validPassword]) => ({
    email: validEmail,
    password: validPassword
  }))
}
```

**Sequential validation:**

```typescript
function createAccount(
  email: string,
  password: string
): Result<Account, string> {
  return validateEmail(email)
    .andThen(validEmail =>
      validatePassword(password)
        .map(validPassword => ({
          email: validEmail,
          password: validPassword
        }))
    )
}
```

## Workflow: Applying Result Types to Existing Code

### Step 1: Identify Error-Prone Operations

Scan code for operations that can fail:
- File system operations
- Network requests
- Parsing operations (JSON, XML, etc.)
- Database queries
- Validation logic
- Division or mathematical operations
- Array access with potential undefined results

### Step 2: Define Error Types

Create specific error types for better type safety:

```typescript
type ValidationError =
  | { type: "missing_field"; field: string }
  | { type: "invalid_format"; field: string; message: string }
  | { type: "out_of_range"; field: string; min: number; max: number }

type DatabaseError =
  | { type: "connection_failed"; message: string }
  | { type: "query_failed"; query: string; message: string }
  | { type: "not_found"; id: string }

type AppError = ValidationError | DatabaseError
```

### Step 3: Transform Functions

Convert each error-prone function to return `Result<T, E>`:

1. Change return type from `T` to `Result<T, ErrorType>`
2. Replace `throw` statements with `err(error)`
3. Replace successful returns with `ok(value)`
4. Wrap external throwing code with `fromThrowable`

### Step 4: Update Call Sites

Update all code that calls the transformed functions:

```typescript
// Before
try {
  const user = loadUser(id)
  processUser(user)
} catch (error) {
  handleError(error)
}

// After
const userResult = loadUser(id)
if (userResult.isOk()) {
  processUser(userResult.value)
} else {
  handleError(userResult.error)
}
```

### Step 5: Verify No Remaining Throws

Search codebase for remaining exception patterns:
- `throw new Error`
- `throw new`
- bare `throw` statements
- Uncaught Promise rejections

Replace all with Result type returns.

## Best Practices

### 1. Use Specific Error Types

Avoid generic `Error` types. Define domain-specific error unions:

```typescript
// Good: Specific error types
type UserError =
  | { type: "not_found"; id: string }
  | { type: "invalid_id"; id: string }
  | { type: "permission_denied"; userId: string; resource: string }

function getUser(id: string): Result<User, UserError> { ... }

// Avoid: Generic Error
function getUser(id: string): Result<User, Error> { ... }
```

### 2. Make Error Handling Exhaustive

Use TypeScript's discriminated unions for exhaustive error handling:

```typescript
const result = performOperation()
if (result.isErr()) {
  switch (result.error.type) {
    case "not_found":
      return handleNotFound(result.error.id)
    case "invalid_id":
      return handleInvalidId(result.error.id)
    case "permission_denied":
      return handlePermissionDenied(result.error)
    // TypeScript ensures all cases covered
  }
}
```

### 3. Preserve Error Context

Include relevant context in error values:

```typescript
// Good: Rich error context
return err({
  type: "validation_failed",
  field: "email",
  value: input,
  message: "Email must contain @"
})

// Avoid: Minimal context
return err("Validation failed")
```

### 4. Use Helper Functions for Common Patterns

Create reusable wrappers for common operations:

```typescript
import { Result, ok, err, fromThrowable } from "neverthrow"

const safeParseInt = (input: string): Result<number, string> => {
  const num = parseInt(input, 10)
  return isNaN(num) ? err("Not a number") : ok(num)
}

const safeArrayAccess = <T>(
  arr: T[],
  index: number
): Result<T, string> => {
  return index >= 0 && index < arr.length
    ? ok(arr[index])
    : err(`Index ${index} out of bounds`)
}
```

### 5. Document Expected Errors

Document which errors functions can return:

```typescript
/**
 * Load user from database
 *
 * @returns Result containing User or error:
 *   - "not_found": User does not exist
 *   - "invalid_id": ID format is invalid
 *   - "db_error": Database connection failed
 */
function loadUser(id: string): Result<User, UserError> {
  // ...
}
```

## Anti-Patterns to Avoid

### ❌ Don't Throw in Result-Returning Functions

```typescript
// WRONG
function parseNumber(input: string): Result<number, string> {
  if (input === "") {
    throw new Error("Empty input") // Don't throw!
  }
  return ok(parseInt(input))
}

// CORRECT
function parseNumber(input: string): Result<number, string> {
  if (input === "") {
    return err("Empty input") // Return error
  }
  return ok(parseInt(input))
}
```

### ❌ Don't Ignore Result Values

```typescript
// WRONG
loadUser(id) // Result ignored

// CORRECT
const result = loadUser(id)
if (result.isOk()) {
  processUser(result.value)
}
```

### ❌ Don't Use try-catch for Control Flow

```typescript
// WRONG
try {
  const result = safeOperation()
  if (result.isErr()) {
    throw result.error // Don't throw Results!
  }
} catch (e) {
  handleError(e)
}

// CORRECT
const result = safeOperation()
if (result.isErr()) {
  handleError(result.error)
  return
}
processSuccess(result.value)
```

### ❌ Don't Mix Exceptions and Results

```typescript
// WRONG: Mixing patterns
function mixedPattern(): Result<Data, Error> {
  if (condition) {
    throw new Error("Bad!") // Don't mix!
  }
  return ok(data)
}

// CORRECT: Consistent Result usage
function consistentPattern(): Result<Data, Error> {
  if (condition) {
    return err(new Error("Bad!"))
  }
  return ok(data)
}
```

## Integration with External Libraries

### Wrapping Third-Party APIs

Many external libraries throw exceptions. Wrap them with `fromThrowable`:

```typescript
import { fromThrowable } from "neverthrow"
import fs from "fs"

// Wrap Node.js fs operations
const safeReadFile = fromThrowable(
  (path: string, encoding: BufferEncoding) =>
    fs.readFileSync(path, encoding),
  (error) => ({
    type: "fs_error" as const,
    message: String(error)
  })
)

const safeWriteFile = fromThrowable(
  (path: string, data: string) =>
    fs.writeFileSync(path, data),
  (error) => ({
    type: "fs_error" as const,
    message: String(error)
  })
)

// Use wrapped functions
function loadConfig(path: string): Result<Config, FsError> {
  return safeReadFile(path, "utf-8")
    .andThen(content => safeParseJSON(content))
}
```

### Async Operations with ResultAsync.fromPromise

Wrap async operations that might reject:

```typescript
import { ResultAsync } from "neverthrow"

const safeFetch = (url: string, options?: RequestInit) =>
  ResultAsync.fromPromise(
    fetch(url, options).then(async response => {
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`)
      }
      return response
    }),
    (error) => ({
      type: "network_error" as const,
      message: error instanceof Error ? error.message : String(error)
    })
  )

function getUser(id: string): ResultAsync<User, NetworkError> {
  return safeFetch(`/api/users/${id}`)
    .andThen(response =>
      ResultAsync.fromPromise(
        response.json(),
        () => ({ type: "network_error" as const, message: "Failed to parse response" })
      )
    )
    .map(data => data as User)
}
```

## Testing Result-Based Code

### Test Both Success and Failure Paths

```typescript
import { describe, it, expect } from "vitest"

describe("parseAge", () => {
  it("should return ok for valid age", () => {
    const result = parseAge("25")
    expect(result.isOk()).toBe(true)
    if (result.isOk()) {
      expect(result.value).toBe(25)
    }
  })

  it("should return err for invalid number", () => {
    const result = parseAge("abc")
    expect(result.isErr()).toBe(true)
    if (result.isErr()) {
      expect(result.error).toBe("Invalid number")
    }
  })

  it("should return err for age out of range", () => {
    const result = parseAge("200")
    expect(result.isErr()).toBe(true)
    if (result.isErr()) {
      expect(result.error).toBe("Age out of range")
    }
  })
})
```

### Test Error Type Discrimination

```typescript
it("should handle different error types", () => {
  const result = loadUser("invalid-id")
  expect(result.isErr()).toBe(true)

  if (result.isErr()) {
    switch (result.error.type) {
      case "not_found":
        expect(result.error.id).toBe("invalid-id")
        break
      case "invalid_id":
        expect(result.error.id).toBe("invalid-id")
        break
      default:
        throw new Error("Unexpected error type")
    }
  }
})
```

## Quick Reference

### Key Functions

| Function | Description |
|----------|-------------|
| `ok(value)` | Create success Result |
| `err(error)` | Create failure Result |
| `result.isOk()` | Type guard for success |
| `result.isErr()` | Type guard for failure |
| `result.value` | Access success value (after isOk check) |
| `result.error` | Access error value (after isErr check) |
| `result.map(fn)` | Transform success value |
| `result.mapErr(fn)` | Transform error value |
| `result.andThen(fn)` | Chain Result-returning operations |
| `Result.combine([...])` | Combine multiple Results |
| `fromThrowable(fn, errorHandler)` | Wrap throwing function |
| `ResultAsync.fromPromise(promise, errorHandler)` | Wrap Promise |

### Common Patterns Summary

1. **Basic transformation**: `throw Error` → `return err(error)`
2. **Success return**: `return value` → `return ok(value)`
3. **Checking results**: `try-catch` → `if (result.isOk()) { ... } else { ... }`
4. **Wrapping externals**: `externalFn()` → `fromThrowable(externalFn, handler)()`
5. **Async wrapping**: `await promise` → `ResultAsync.fromPromise(promise, handler)`
6. **Chaining**: `result.andThen(fn).map(fn2).mapErr(fn3)`

## Resources

- Official neverthrow documentation: https://github.com/supermacro/neverthrow
- Reference guide: https://raw.githubusercontent.com/mizchi/ts-guide/refs/heads/main/docs/ts-guide/error-handling.md

## Additional Notes

- Result types make error handling **explicit** rather than implicit
- All failure paths become **visible** in function signatures
- Type system **enforces** error handling at compile time
- Reduces runtime surprises by making errors **part of the type**
- Improves code **maintainability** and **debuggability**

When applying this skill, prioritize clarity and type safety. Make every error path explicit, use discriminated unions for error types, and ensure exhaustive error handling throughout the codebase.
