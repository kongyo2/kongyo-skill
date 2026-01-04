---
name: oxlint-setup
description: This skill should be used when setting up oxlint in a new TypeScript project. It provides a complete workflow for installing oxlint, creating configuration files, and integrating it into the project's development workflow. The skill fetches the latest documentation to ensure up-to-date and accurate setup instructions, preventing outdated configuration advice.
---

# Oxlint Setup

## Overview

This skill guides the complete setup of oxlint in a TypeScript project. oxlint is a Rust-based linter that provides extremely fast linting with minimal configuration, encompassing most essential ESLint and TypeScript-specific rules.

The skill follows a documentation-driven approach: instead of embedding potentially outdated configuration examples, it fetches the latest documentation at execution time to ensure current and accurate setup instructions.

## When to Use This Skill

Use this skill when:
- Setting up oxlint in a new TypeScript project
- The user requests "setup oxlint", "install oxlint", or "configure oxlint"
- A project needs linting infrastructure and fast performance is desired
- Migrating from ESLint to oxlint (though verify migration-specific requirements)

## Setup Workflow

### Step 1: Fetch Latest Documentation

Before proceeding with any setup steps, fetch the latest documentation to ensure accurate and up-to-date configuration:

**Required documentation:**
1. Primary reference - TypeScript linting guide:
   ```
   https://raw.githubusercontent.com/mizchi/ts-guide/refs/heads/main/docs/ts-guide/linter.md
   ```
   Use WebFetch to retrieve this document, focusing on oxlint setup and configuration instructions.

2. Official oxlint configuration documentation:
   ```
   https://oxc.rs/docs/guide/usage/linter/config.html
   ```
   Use WebFetch to retrieve the complete configuration guide, including setup, configuration file format, and usage instructions.

**Additional documentation to fetch as needed:**

Based on the project's requirements, fetch additional documentation from oxc.rs as appropriate:
- Rules configuration: `https://oxc.rs/docs/guide/usage/linter/rules.html`
- Plugin system: `https://oxc.rs/docs/guide/usage/linter/plugins.html`
- CLI options: `https://oxc.rs/docs/guide/usage/linter/cli.html`
- Generated configuration reference: `https://oxc.rs/docs/guide/usage/linter/generated-config.html`

Always fetch documentation before making recommendations to ensure the latest best practices are followed.

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

Create `.oxlintrc.json` in the project root. The configuration structure should be derived from the fetched documentation.

**Key configuration sections** (refer to fetched docs for current syntax):
- `plugins`: Framework or file type extensions
- `categories`: Rule category severity levels (correctness, suspicious, pedantic, perf, style, etc.)
- `rules`: Specific rule configurations
- `ignorePatterns`: Files/directories to exclude from linting
- `env`: Global variables based on environment
- `settings`: Plugin-specific configuration

**Configuration approach:**
1. Reference the fetched documentation for current best practices
2. Start with recommended defaults from the TypeScript guide
3. Customize based on project needs
4. Include common ignore patterns (node_modules, dist, build, coverage, etc.)

### Step 5: Add npm Scripts

Add linting scripts to `package.json`. Based on current best practices (verify with fetched docs), typical scripts include:

- Standard linting command
- Strict mode with warnings as errors
- File-specific checking capability
- Integration with other development scripts (e.g., pre-commit hooks)

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

While oxlint works with zero configuration, projects may need customization:
- Inline comments for specific code sections (`/* oxlint-disable rule-name */`)
- CLI options for one-off overrides
- Category-level configuration for broad rule groups

### Migration Notes

If migrating from ESLint:
- Consult fetched documentation for migration guidance
- Note that not all ESLint plugins have oxlint equivalents
- Consider keeping ESLint for rules not covered by oxlint
- Test thoroughly before removing ESLint entirely

## Documentation URLs Reference

**Primary references (always fetch):**
- TypeScript guide: `https://raw.githubusercontent.com/mizchi/ts-guide/refs/heads/main/docs/ts-guide/linter.md`
- Configuration guide: `https://oxc.rs/docs/guide/usage/linter/config.html`

**Supplementary references (fetch as needed):**
- Rules: `https://oxc.rs/docs/guide/usage/linter/rules.html`
- Plugins: `https://oxc.rs/docs/guide/usage/linter/plugins.html`
- CLI: `https://oxc.rs/docs/guide/usage/linter/cli.html`
- Config reference: `https://oxc.rs/docs/guide/usage/linter/generated-config.html`

Always verify URLs are current and fetch fresh documentation for each setup to avoid outdated advice.
