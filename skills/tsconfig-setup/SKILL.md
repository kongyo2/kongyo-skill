---
name: tsconfig-setup
description: Set up and optimize tsconfig.json for TypeScript projects based on the latest community best practices. Use this skill when the user requests TypeScript project configuration, tsconfig.json setup, TypeScript compiler options optimization, or asks for recommended TypeScript settings for their project.
---

# TypeScript Configuration Setup

## Overview

Set up and optimize tsconfig.json for TypeScript projects by fetching and applying the latest community-maintained best practices. This skill ensures TypeScript configurations remain current with modern recommendations without risk of obsolescence.

## When to Use This Skill

Use this skill when the user requests:
- "Set up tsconfig.json for my TypeScript project"
- "Optimize my TypeScript configuration"
- "What are the recommended TypeScript compiler options?"
- "Create a tsconfig.json with best practices"
- "Update my tsconfig.json to modern standards"
- Any request related to TypeScript project configuration or compiler settings

## Workflow

### Step 1: Read the TypeScript Configuration Guide

**Always start by reading the configuration guide below** to ensure recommendations reflect the latest best practices.

---

## TypeScript Configuration Guide

### Base Configuration

The baseline setup uses the following `tsconfig.json` configuration:

```json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "moduleResolution": "bundler",
    "noEmit": true,
    "esModuleInterop": true,
    "allowImportingTsExtensions": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "types": ["vitest/importMeta"]
  }
}
```

### Key Configuration Options

- `target: "esnext"`: Compile to the latest JavaScript features
- `module: "esnext"`: Use ES modules
- `moduleResolution: "bundler"`: Modern module resolution for bundlers
- `allowImportingTsExtensions`: Allow `.ts` extensions in imports (for Deno compatibility)
- `strict: true`: Enable all strict type checking options
- `skipLibCheck`: Skip type checking of declaration files for faster builds
- `noUnusedLocals: true`: Error on unused local variables
- `noUnusedParameters: false`: Allow unused function parameters (useful for interface implementations)

### Performance Optimization with TypeScript Native Preview (Optional)

**When to use**: Large codebases with slow type checking
**Recommended**: No (experimental, use with caution)

#### Installation

```bash
pnpm add @typescript/native-preview -D
```

#### Usage

Replace `tsc` with `tsgo` in your scripts:

```json
{
  "scripts": {
    "typecheck": "tsgo --noEmit"
  }
}
```

#### VS Code Integration

While you can enable tsgo in VS Code with the following setting, **this is not recommended**:

```json
// .vscode/settings.json
{
  "typescript.experimental.useTsgo": true
}
```

**Why not recommended**: Using tsgo in the IDE can lead to inconsistencies between your editor and the standard TypeScript compiler.

#### Benefits

- **Faster Type Checking**: The native implementation can be several times faster than the standard TypeScript compiler
- **Drop-in Replacement**: Works with existing TypeScript configurations
- **Experimental Features**: Access to cutting-edge TypeScript features before they land in the stable release

#### Important Considerations

- This is an experimental preview and may have bugs or missing features
- **Only use for the `typecheck` script**, not for builds or IDE integration
- **If you encounter any differences between `tsc` and `tsgo` output, stop using tsgo immediately**
- Not recommended for production builds
- Best suited for development-time type checking to improve iteration speed

#### Recommended Approach

```json
{
  "scripts": {
    "typecheck": "tsc --noEmit",
    "typecheck:fast": "tsgo --noEmit"
  }
}
```

Use `typecheck:fast` during development if you need faster feedback, but always verify with standard `tsc` before committing.

### Advanced Configuration Options

#### Strict Mode Options

**When to use**: New projects
**Recommended**: Yes (use `"strict": true`)

```json
{
  "compilerOptions": {
    "strict": true,
    // Or configure individually:
    "noImplicitAny": true,
    "noImplicitThis": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true
  }
}
```

#### Output Configuration

**When to use**: Library packages
**Recommended**: Yes for libraries, No for applications

```json
{
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  }
}
```

### Building Without Bundlers

**When to use**: Library packages without bundling
**Recommended**: Yes for publishable libraries

#### Separate Output for JavaScript and Type Declarations

```json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "moduleResolution": "bundler",
    "rootDir": "./src",
    "outDir": "./dist",
    "declarationDir": "./dist-types",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "allowImportingTsExtensions": false
  },
  "include": ["src/**/*"],
  "exclude": ["**/*.test.ts", "**/*.spec.ts"]
}
```

**Note**: When building for distribution, set `allowImportingTsExtensions: false` and remove `.ts` extensions from imports.

#### Package.json Exports Configuration

Configure your `package.json` to properly expose both JavaScript and TypeScript types:

```json
{
  "name": "@your-scope/package-name",
  "version": "1.0.0",
  "type": "module",
  "exports": {
    ".": {
      "types": "./dist-types/index.d.ts",
      "import": "./dist/index.js",
      "default": "./dist/index.js"
    },
    "./utils": {
      "types": "./dist-types/utils.d.ts",
      "import": "./dist/utils.js",
      "default": "./dist/utils.js"
    },
    "./package.json": "./package.json"
  },
  "main": "./dist/index.js",
  "types": "./dist-types/index.d.ts",
  "files": ["dist", "dist-types", "!**/*.test.*", "!**/*.spec.*"],
  "scripts": {
    "build": "tsc",
    "build:watch": "tsc --watch",
    "clean": "rm -rf dist dist-types"
  }
}
```

#### Build Scripts

```json
{
  "scripts": {
    "build": "tsc",
    "clean": "rm -rf dist dist-types",
    "prepublishOnly": "pnpm run clean && pnpm run build"
  }
}
```

#### Advanced Exports with Subpath Patterns

**When to use**: Multi-entry point libraries
**Recommended**: Optional

```json
{
  "exports": {
    ".": {
      "types": "./dist-types/index.d.ts",
      "import": "./dist/index.js"
    },
    "./*": {
      "types": "./dist-types/*.d.ts",
      "import": "./dist/*.js"
    },
    "./components": {
      "types": "./dist-types/components/index.d.ts",
      "import": "./dist/components/index.js"
    },
    "./components/*": {
      "types": "./dist-types/components/*.d.ts",
      "import": "./dist/components/*.js"
    }
  }
}
```

---

### Step 2: Analyze the Project Context

Before recommending configuration, understand the project requirements:

#### Project Type Detection
Identify whether the project is:
- **Application**: End-user application, web app, CLI tool, server
- **Library**: Package intended for distribution on npm or other registries
- **Monorepo**: Multiple packages in one repository
- **Experimental/Learning**: Side project, tutorial, or proof of concept

**Methods to detect project type:**
1. Check package.json for indicators:
   - Presence of `"main"`, `"module"`, `"exports"` suggests library
   - Scripts like `"start"`, `"dev"`, `"serve"` suggest application
   - `"private": true` typically indicates application
2. Examine directory structure
3. Ask the user if unclear

#### Existing Configuration Analysis
If tsconfig.json already exists:
1. Read the current configuration
2. Identify potentially outdated or suboptimal settings
3. Note any project-specific customizations to preserve
4. Check for conflicting or redundant options

#### Dependencies and Environment
Consider:
- Target runtime environment (Node.js version, browser support)
- Framework requirements (React, Vue, Next.js, etc.)
- Build tool expectations (Vite, webpack, esbuild, tsc only)
- Testing framework compatibility

### Step 3: Design the Configuration

Based on the fetched guide and project analysis, design an appropriate tsconfig.json.

#### Core Principles

**For Applications:**
- Prioritize strict type checking for code quality
- Enable performance optimizations for faster development feedback
- Configure for the target runtime environment
- Optimize for development experience

**For Libraries:**
- Enable declaration generation (`"declaration": true`)
- Configure proper module output formats
- Ensure compatibility with consumer toolchains
- Set up source maps for debugging

#### Essential Configuration Sections

Structure the tsconfig.json with these sections:

1. **Compiler Options** - Core TypeScript behavior
2. **Include/Exclude** - Which files to process
3. **Module Resolution** - How imports are resolved
4. **Type Checking** - Strictness and safety options
5. **Emit Options** - Output configuration
6. **Performance** - Build speed optimizations

#### Apply Best Practices from Guide

Implement recommendations from the fetched guide, typically including:

- **Modern targets**: Use recent ECMAScript versions unless compatibility requires otherwise
- **Strict mode**: Enable `"strict": true` for maximum type safety
- **Module system**: Choose appropriate module format (ESNext, CommonJS, etc.)
- **Path resolution**: Configure `baseUrl`, `paths` if needed
- **Skip lib check**: Often `"skipLibCheck": true` for faster builds
- **Source maps**: Enable for debugging (`"sourceMap": true`)

#### Project-Specific Customizations

Common customizations by project type:

**React Projects:**
```json
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "lib": ["DOM", "DOM.Iterable", "ESNext"]
  }
}
```

**Node.js Backend:**
```json
{
  "compilerOptions": {
    "module": "CommonJS",
    "target": "ES2020",
    "lib": ["ES2020"]
  }
}
```

**Library Distribution:**
```json
{
  "compilerOptions": {
    "declaration": true,
    "declarationMap": true,
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

### Step 4: Implement the Configuration

#### Creating New Configuration

If no tsconfig.json exists:
1. Create the file at project root
2. Write the complete configuration with comments explaining key decisions
3. Organize with clear section headers for readability

**Example structure with documentation:**
```json
{
  "compilerOptions": {
    // Language and Environment
    "target": "ES2020",
    "lib": ["ES2020"],

    // Modules
    "module": "ESNext",
    "moduleResolution": "bundler",

    // Type Checking
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,

    // Emit
    "outDir": "./dist",
    "sourceMap": true,

    // Performance
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

#### Updating Existing Configuration

If tsconfig.json exists:
1. Preserve user-specific customizations when reasonable
2. Update outdated options to modern equivalents
3. Add missing recommended options
4. Remove deprecated or redundant settings
5. Add comments explaining changes made

Use Edit tool to make surgical updates rather than wholesale replacement when possible.

#### Validation

After implementation:
1. Verify the JSON is valid
2. Ensure no conflicting options
3. Check that included/excluded paths are correct
4. Consider running `tsc --noEmit` to validate configuration if possible

### Step 5: Explain and Document

Provide the user with:

1. **Summary of changes**: Brief overview of what was configured
2. **Rationale**: Why specific options were chosen based on project type
3. **Key features**: Highlight important compiler options and their benefits
4. **Next steps**: Any additional setup or recommendations

**Example explanation:**
```
Created tsconfig.json with modern best practices for your Node.js application:

Key Settings:
- Enabled strict mode for maximum type safety
- Configured ES2020 target for modern Node.js
- Set up source maps for debugging
- Enabled performance optimization with skipLibCheck

The configuration prioritizes code quality and development speed while matching
your Node.js runtime environment.
```

#### Common Recommendations

Based on the configuration, might recommend:
- Installing type definitions (`@types/*` packages)
- Setting up development scripts in package.json
- Configuring ESLint for consistency with TypeScript settings
- Considering TypeScript project references for monorepos

## Important Notes

- **Read the embedded guide**: The TypeScript Configuration Guide is embedded in Step 1 above
- **Context matters**: Applications and libraries require different configurations
- **Explain choices**: Help users understand why specific options are recommended
- **Validate output**: Ensure generated configurations are syntactically correct JSON
