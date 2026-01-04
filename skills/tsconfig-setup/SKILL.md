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

### Step 1: Fetch the Latest TypeScript Guide

**Always start by fetching the most current guidance** to ensure recommendations reflect the latest best practices.

Use WebFetch to retrieve the authoritative TypeScript configuration guide:

```
URL: https://raw.githubusercontent.com/mizchi/ts-guide/refs/heads/main/docs/ts-guide/typescript.md
```

**WebFetch prompt should request:**
- Recommended base tsconfig.json settings for modern TypeScript projects
- Compiler options and their purposes
- Project type-specific configurations (application vs library distribution)
- Performance optimization techniques
- Strict mode and type checking recommendations
- Module resolution best practices
- Any deprecated or discouraged settings to avoid

**Why fetch every time:**
- TypeScript and its ecosystem evolve rapidly
- Ensures recommendations never become outdated
- Incorporates latest performance optimizations
- Reflects current community consensus

For reference, see `references/typescript_guide.md` for additional context on the guide structure.

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

## Resources

### references/

**typescript_guide.md** - Documentation about the primary TypeScript configuration guide and how to use it within this workflow. Contains the URL to fetch and context about why the remote reference approach ensures the skill never becomes outdated.

## Important Notes

- **Always fetch the guide fresh**: Never rely on cached or remembered configuration advice
- **Context matters**: Applications and libraries require different configurations
- **Explain choices**: Help users understand why specific options are recommended
- **Stay current**: The remote guide approach ensures recommendations never become stale
- **Validate output**: Ensure generated configurations are syntactically correct JSON
