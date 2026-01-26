---
name: oxlint-setup
description: This skill should be used when setting up oxlint in a new TypeScript project. It provides a complete workflow for installing oxlint, creating configuration files, and integrating it into the project's development workflow. The configuration specified in this skill is mandatory and must be applied exactly as documented.
---

# Oxlint Setup

## Overview

This skill guides the complete setup of oxlint in a TypeScript project. oxlint is a Rust-based linter that provides extremely fast linting with minimal configuration, encompassing most essential ESLint and TypeScript-specific rules.

## When to Use This Skill

Use this skill when:
- Setting up oxlint in a new TypeScript project
- The user requests "setup oxlint", "install oxlint", or "configure oxlint"
- A project needs linting infrastructure and fast performance is desired
- Migrating from ESLint to oxlint (though verify migration-specific requirements)

## Setup Workflow

### Step 1: Read the Linter Setup Guide

Before proceeding with any setup steps, read the embedded guide below carefully. **All configuration must be applied exactly as documented.**

---

## Linter Setup Guide

### oxlint (Recommended)

#### Overview

oxlint is a Rust-based linter that provides extremely fast linting with zero configuration. It offers:

- **Blazing Fast Performance**: Written in Rust, it's significantly faster than JavaScript-based linters
- **Comprehensive Rule Set**: Includes most essential ESLint rules and TypeScript-specific rules
- **Zero Configuration**: Works out of the box with sensible defaults
- **Growing Ecosystem**: Actively maintained with regular updates and new rules

#### Setup

```bash
# Install oxlint
pnpm add -D oxlint
```

#### Package.json Scripts

```json
{
  "scripts": {
    "lint": "oxlint --silent",
    "lint:strict": "oxlint --deny-warnings",
    "check:file": "oxlint"
  }
}
```

> **Note**: The `check:file` command allows linting specific files: `pnpm check:file src/index.ts`

#### Configuration (.oxlintrc.json)

> **IMPORTANT**: This configuration is mandatory and must be applied exactly as shown. Do not weaken, omit, or modify any settings. The plugins, categories, and rules specified here are carefully chosen for code quality enforcement.

```json
{
  "$schema": "./node_modules/oxlint/configuration_schema.json",
  "plugins": ["eslint", "typescript", "unicorn", "oxc", "import", "promise", "node"],
  "categories": {
    "correctness": "error",
    "suspicious": "warn",
    "perf": "warn"
  },
  "rules": {
    "no-console": "warn",
    "typescript/no-explicit-any": "error"
  },
  "ignorePatterns": ["node_modules", "dist", "build", "coverage", "*.min.js"]
}
```

**Plugin explanation:**
- `eslint`, `typescript`, `unicorn`, `oxc`: Default plugins (must be explicitly listed when using the plugins field, otherwise they are disabled)
- `import`: Module import/export validation
- `promise`: Promise handling best practices
- `node`: Node.js environment rules

#### CI Integration

```yaml
# In .github/workflows/ci.yaml
- run: pnpm lint
```

### Selection Guidelines

#### Choose oxlint when:

- Performance is critical
- You want zero configuration
- You need fast CI/CD feedback
- Your project uses standard linting rules

### Integration with check script

When adding a linter, update the main `check` script to include linting:

```json
{
  "scripts": {
    "check": "pnpm typecheck && pnpm test && pnpm format:check && pnpm lint"
  }
}
```

### Best Practices for oxlint

- Use for performance-critical projects
- Combine with other tools for comprehensive checking
- Ideal for large codebases
- Good for CI/CD pipelines

### Troubleshooting oxlint

- **Missing rules**: Check oxlint documentation for supported rules
- **Configuration**: Ensure .oxlintrc.json is valid JSON
- **File patterns**: Use correct glob patterns for file matching

---

### Step 2: Analyze Project Structure

Before installation, examine the project to understand:
- Package manager in use (npm, pnpm, yarn, bun)
- Existing linting configuration (ESLint, Biome, etc.)
- TypeScript configuration (tsconfig.json)
- Project structure and directories to ignore

Use Glob and Read tools to inspect:
- `package.json` - Check existing dependencies and scripts
- `tsconfig.json` - Understand TypeScript configuration
- Existing linter configs - Identify any conflicts or migration needs

### Step 3: Install oxlint

Install oxlint as a dev dependency using the appropriate package manager:

```bash
# Examples (choose based on project's package manager):
npm install -D oxlint
pnpm add -D oxlint
yarn add -D oxlint
bun add -D oxlint
```

Verify installation by checking `package.json` devDependencies.

### Step 4: Create Configuration File

Create `.oxlintrc.json` in the project root using **exactly** the configuration specified in the "Configuration (.oxlintrc.json)" section above.

> **CRITICAL**: Do not modify, weaken, or omit any part of the specified configuration. The configuration must be copied exactly as documented. Disabling rules, removing plugins, or changing severity levels to avoid lint errors is strictly prohibited. If lint errors occur, fix the code instead of changing the configuration.

**Key configuration sections**:
- `$schema`: Enables editor autocompletion and validation
- `plugins`: Must include all default plugins (`eslint`, `typescript`, `unicorn`, `oxc`) plus additional plugins (`import`, `promise`, `node`)
- `categories`: Rule category severity levels - do not lower these
- `rules`: Specific rule configurations - do not disable these
- `ignorePatterns`: Files/directories to exclude from linting

**Configuration approach:**
1. Copy the exact configuration from the "Configuration (.oxlintrc.json)" section above
2. Do not customize or weaken any settings
3. Include common ignore patterns (node_modules, dist, build, coverage, etc.)

### Step 5: Add npm Scripts

Add linting scripts to `package.json`. The required scripts are:

- `lint`: Standard linting command (`oxlint --silent`)
- `lint:strict`: Strict mode with warnings as errors (`oxlint --deny-warnings`)
- `check:file`: File-specific checking capability (`oxlint`)

Use the Edit tool to add scripts to the existing `package.json`.

### Step 6: Verify Setup

After configuration:
1. Run the lint command to verify oxlint executes correctly
2. Test on actual project files to ensure rules are working
3. Check that ignore patterns are properly excluding files
4. Confirm no conflicts with existing tooling

Report any errors and iterate on configuration as needed.

## Additional Considerations

### Performance Benefits

Highlight oxlint's performance advantages when presenting the setup:
- Significantly faster than JavaScript-based linters
- Zero-config default behavior
- Ideal for CI/CD pipelines requiring rapid feedback

### Configuration Flexibility

While oxlint works with zero configuration, projects may need customization for ignore patterns only:
- Inline comments for specific code sections (`/* oxlint-disable rule-name */`)
- CLI options for one-off overrides
- Category-level configuration for broad rule groups

> **Note**: Configuration flexibility should only be used to add additional restrictions or modify ignore patterns. Never use it to weaken the mandatory configuration.

### Migration Notes

If migrating from ESLint:
- Note that not all ESLint plugins have oxlint equivalents
- Consider keeping ESLint for rules not covered by oxlint
- Test thoroughly before removing ESLint entirely
