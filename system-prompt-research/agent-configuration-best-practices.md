# Best Practices for Creating Agent Configuration Files Across Vibe Coding Apps and Specific Tools

Based on comprehensive research across all major vibe coding applications, here are the definitive best practices for configuring AI coding agents, organized from universal principles to tool-specific approaches. This includes general best practices and specific guidance for tools like OpenAI Codex and OpenCode.

## Executive Summary: The Commonalities

**All vibe coding apps share these core configuration elements:**

1. **Markdown format** for natural language instructions
2. **Project context** sections (overview, structure, tech stack)
3. **Do's and Don'ts** for clear positive/negative guidance
4. **Concrete examples** over abstract rules
5. **Command specifications** for builds, tests, and linting
6. **Permission boundaries** defining auto-approved vs. ask-first actions
7. **Iterative refinement** - all benefit from continuous improvement

**The emerging universal standard: AGENTS.md** - adopted by 20,000+ open-source projects and supported natively by OpenAI Codex, Google Jules, GitHub Copilot, OpenCode, and compatible with all others via symlinks or references.[^1][^2][^3][^4]

## Universal Best Practices (Apply to All Tools)

### Core Configuration Principles

**Single Source of Truth**: Use AGENTS.md as your primary configuration file when possible. This vendor-neutral format works across the entire ecosystem and reduces maintenance overhead.[^2][^5][^1]

**Clarity Over Brevity**: Be explicit and specific. Instead of "write good code," specify "Use TypeScript strict mode with exactOptionalPropertyTypes enabled".[^6][^1]

**Concrete Examples**: Point to actual files in your codebase. "Copy patterns from `app/components/GoodExample.tsx`, avoid `Admin.tsx` (legacy)" beats abstract guidance.[^1][^6]

**File-Scoped Commands**: Specify file-level checks rather than full project builds. Use `npm run tsc --noEmit path/to/file.tsx` instead of `npm run build` for faster feedback loops.[^6][^1]

**Iterative Refinement**: Add rules as you encounter issues. The second time an agent makes the same mistake, add a rule to prevent it.[^1][^6]

### Essential Sections for Any Agent Configuration

Every configuration file should include these 10 core sections:[^6][^1]

1. **Project Overview** - Tech stack, versions, architecture patterns
2. **Do's and Don'ts** - Preferred patterns and anti-patterns
3. **File Structure** - Directory layout and component locations
4. **Commands** - Testing, linting, formatting, build commands
5. **Code Style** - Naming conventions, import patterns, state management
6. **Safety \& Permissions** - Auto-approved actions vs. ask-first
7. **Testing Requirements** - Frameworks, coverage, test patterns
8. **Good/Bad Examples** - Reference files to emulate or avoid
9. **API Documentation** - Locations and usage patterns
10. **PR Guidelines** - Title formats, required checks, standards

### Character Limits and File Organization

**Recommended limits by tool:**

- **Windsurf**: 12,000 characters per rule file (hard limit)[^7][^8]
- **General recommendation**: Keep under 6,000-8,000 characters per file[^1][^6]
- **Cursor, Cline, GitHub Copilot**: No strict limits, but focused files work better

**Multi-file strategy**: For large configurations, split into focused files by concern rather than creating one massive document.[^9][^10][^11]

## AGENTS.md: The Universal Standard

### Why AGENTS.md is Becoming the Standard

AGENTS.md emerged from collaboration between OpenAI, Google, Cursor, Sourcegraph (Amp), and Factory to solve a real problem: developers maintaining separate configuration files for every AI tool (`.cursorrules`, `.windsurfrules`, `CLAUDE.md`, `.clinerules`, etc.).[^3][^5][^4][^2][^1]

**Benefits:**

- **Tool agnostic** - works with 20+ AI coding tools
- **Vendor neutral** - domain owned by agent-rules.org
- **Version controlled** - standard markdown file in your repo
- **Reduces clutter** - one file instead of many tool-specific configs
- **Nested support** - hierarchical rules for monorepos

### AGENTS.md Template Structure

```markdown
# AGENTS.md

## Project Overview
[Brief description of project purpose and architecture]
Tech Stack: Next.js 14, React 18, TypeScript 5.2, Tailwind CSS

## Do's
- Use TypeScript strict mode with exactOptionalPropertyTypes
- Use functional components with hooks
- Use Tailwind for all styling (no inline styles)
- Keep components under 200 lines
- Write tests for new features

## Don'ts
- Do not use class components
- Do not hardcode colors (use design tokens)
- Do not add dependencies without approval
- Do not modify files in /config/secrets

## Commands
# File-scoped checks (preferred)
npm run tsc --noEmit path/to/file.tsx
npm run prettier --write path/to/file.tsx
npm run eslint --fix path/to/file.tsx

# Tests
npm run vitest run path/to/file.test.tsx

# Full build (only when explicitly requested)
npm run build

## Safety and Permissions
Allowed without prompt:
- Read files, list files
- Run file-scoped type checks, linters
- Run single test files

Ask first:
- Package installs
- Git push/force push
- Deleting files
- Full builds or E2E suites

## Project Structure
- Routes: see app/App.tsx
- Components: app/components/
- Styles/tokens: app/lib/theme/tokens.ts
- API client: app/api/client.ts

## Good and Bad Examples
- ✅ Functional components: components/Projects.tsx
- ❌ Class components: components/Admin.tsx (legacy, avoid)
- ✅ Forms: copy app/components/Form.Field.tsx
- ✅ Data fetching: use app/api/client.ts, not direct fetch

## API Documentation
- Docs location: ./api/docs/*.md
- List projects: GET /api/projects via client.projects.list()
- Update project: PATCH /api/projects/:id via client.projects.update()

## PR Checklist
- Title format: feat(scope): description
- All linting and tests pass
- Small, focused diffs with clear summaries
- Documentation updated if needed

## When Stuck
- Ask clarifying questions
- Propose a plan before large changes
- Open draft PR with notes if uncertain
```

### Nested AGENTS.md for Monorepos

For complex projects, use hierarchical AGENTS.md files where more specific rules override general ones:[^4][^2][^1]

```
project-root/
├── AGENTS.md              # Root-level general rules
├── frontend/
│   └── AGENTS.md          # Frontend-specific (React patterns)
├── backend/
│   └── AGENTS.md          # Backend-specific (API patterns)
└── mobile/
    └── AGENTS.md          # Mobile-specific (React Native patterns)
```

Agents read the **nearest** AGENTS.md in the directory tree when working on files.

### Migration to AGENTS.md

**From existing tool-specific configs**, use symlinks for backwards compatibility:

```bash
# Migrate from Cursor
mv .cursorrules AGENTS.md
ln -s AGENTS.md .cursorrules

# Migrate from Cline
mv .clinerules AGENTS.md
ln -s AGENTS.md .clinerules

# Migrate from Windsurf
mv .windsurfrules AGENTS.md
ln -s AGENTS.md .windsurfrules

# Migrate from Claude Code
mv CLAUDE.md AGENTS.md
ln -s AGENTS.md CLAUDE.md
```

**Or use reference approach** for tools that don't support AGENTS.md yet:

```markdown
# CLAUDE.md
Strictly follow the rules in ./AGENTS.md
```

## Tool-Specific Best Practices

### Cursor: Most Sophisticated Configuration

**File structure:**

```
project-root/
└── .cursor/
    └── rules/
        ├── base.mdc
        ├── typescript.mdc
        ├── testing.mdc
        └── api-patterns.mdc
```

**File format with YAML frontmatter:**

```markdown
---
description: TypeScript strict mode patterns
globs: src/**/*.ts
alwaysApply: false
---

# TypeScript Guidelines

Use strict mode with these configurations:
- exactOptionalPropertyTypes
- noUncheckedIndexedAccess
- strict: true

## Example
```typescript
// Good: explicit optional handling
interface User {
  name: string;
  email?: string;  // explicit optional
}

// Bad: implicit any
function process(data) {  // missing types
  return data.value;
}
```
```

**Frontmatter options:**[^12][^9][^6]

- `description`: Brief rule purpose
- `globs`: File pattern for auto-application (e.g., `"**/*.tsx"`)
- `alwaysApply`: Boolean - applies to all contexts

**Four activation modes:**[^12][^9]

1. **Always** - applies regardless of prompt (framework guidelines)
2. **Auto Attached** - applies when files match glob patterns
3. **Agent Requested** - AI decides based on user intent
4. **Manual** - requires explicit `@rule-name` mention

**Best practices:**

- Use kebab-case filenames (`typescript-patterns.mdc`)
- Keep rules modular - separate by concern (one file per domain)
- Use globs for file-specific rules
- Reference rules with `@rule-name` in prompts
- Keep each rule focused (under 6,000 characters)

### Cline: Simple Yet Powerful with Folder System

**Two configuration options:**

**Option 1: Single file** (simple projects)

```
project-root/
└── .clinerules
```

**Option 2: Folder system** (recommended for complex projects)[^13][^14][^10][^15]

```
project-root/
├── .clinerules/
│   ├── 01-coding.md
│   ├── 02-documentation.md
│   └── current-client.md
└── clinerules-bank/              # Optional: inactive rules
    ├── clients/
    │   ├── client-a.md
    │   └── client-b.md
    └── frameworks/
        ├── react.md
        └── vue.md
```

**File format** - pure markdown, no frontmatter:

```markdown
# Project Guidelines

## Coding Standards
- Use TypeScript strict mode
- Prefer functional components
- Use Zod for validation

## Testing Requirements
- Jest for unit tests
- Test coverage > 80%
- Test files: *.test.ts
```

**Folder system workflow** for context switching:[^14][^10]

```bash
# Switch client contexts
rm .clinerules/current-client.md
cp clinerules-bank/clients/client-b.md .clinerules/

# Switch tech stack
rm .clinerules/framework.md
cp clinerules-bank/frameworks/vue.md .clinerules/
```

**Best practices:**

- Use numeric prefixes (`01-`, `02-`) for logical ordering
- Keep active `.clinerules/` folder focused (only relevant rules)
- Use `clinerules-bank/` for inactive context-specific rules
- Move files between folders as context changes
- Version control `.clinerules/` folder
- Can symlink to AGENTS.md: `ln -s AGENTS.md .clinerules`

**Security best practices** for sensitive files:[^16][^13]

```markdown
# Security
## Sensitive Files
DO NOT read or modify:
- .env files
- */config/secrets.*
- **/*.pem
- Any files with API keys, tokens, credentials
```

### Windsurf: XML-Tagged Organization

**File structure:**

```
project-root/
├── .windsurfrules          # Simple single-file approach
└── .windsurf/
    └── rules/
        ├── coding-standards.md
        └── api-patterns.md
```

**File format** - XML-like tags recommended for organization:[^17][^18][^19][^7]

```markdown
<generalCodeStyleFormatting>
Rule: Follow Airbnb Style Guide
Rule: Use TypeScript for all code
Rule: Use PascalCase for React components
Rule: Prefer named exports
</generalCodeStyleFormatting>

<projectStructureArchitecture>
Rule: Follow Next.js App Router patterns
Rule: Place pages under /app
Rule: Components in /components
Restriction: Do not modify /config/secrets
</projectStructureArchitecture>

<stylingUI>
Rule: Use Tailwind CSS
Rule: Use Shadcn UI components
Rule: No inline styles
</stylingUI>

<dataFetchingForms>
Rule: TanStack Query for data fetching
Rule: React Hook Form for forms
Rule: Zod for validation
</dataFetchingForms>
```

**Four activation modes:**[^20][^21][^7]

1. **Manual** - type `@rule-name` to activate
2. **Always On** - applies to every Cascade action
3. **Model Decision** - Cascade decides relevance from description
4. **Glob** - triggers on file path regex (e.g., `*.py`)

**Character limit: 12,000 per file** - split into multiple files if needed.[^8][^7]

**Best practices:**

- Use XML-like tags (`<tagName>`) for clarity and grouping
- Keep rules brief and straightforward
- Use numbered lists or bullet points
- Separate global vs. workspace rules
- Check for conflicts with Global AI Rules first
- Access via Settings > Set Workspace AI Rules > Edit Rules

### GitHub Copilot: Path-Specific Precision

**File structure:**[^22][^23][^24][^25]

```
project-root/
└── .github/
    ├── copilot-instructions.md         # Repository-wide
    └── instructions/
        ├── frontend.instructions.md    # Path-specific
        ├── backend.instructions.md
        └── security.instructions.md
```

**Repository-wide instructions** (`.github/copilot-instructions.md`):

```markdown
# Project Instructions

This is a Next.js 14 project using TypeScript, React 18, and Tailwind.

## Coding Standards
- Use PascalCase for class names
- Use camelCase for variables and functions
- Follow functional patterns over classes

## File Organization
- Interfaces in /types
- Components in feature folders
- Utils in /lib
```

**Path-specific instructions** with YAML frontmatter:[^24][^25]

```markdown
---
applyTo: "app/models/**/*.rb"
---

# Model Instructions
- Use ActiveRecord validations
- Include proper associations
- Write comprehensive tests
```

**Multiple glob patterns:**

```markdown
---
applyTo: "**/*.ts,**/*.tsx"
---

# TypeScript Instructions
- Use strict mode
- No implicit any
```

**Three instruction types:**[^23][^22]

1. **Repository-wide** - `.github/copilot-instructions.md` applies to all requests
2. **Path-specific** - `.github/instructions/*.instructions.md` with `applyTo` glob patterns
3. **Agent instructions** - AGENTS.md files (nearest in tree takes precedence)

**Best practices:**

- Combine repository-wide + path-specific for targeted guidance
- Use glob patterns for precision
- Keep instructions concise and self-contained
- Natural language, markdown format
- Whitespace ignored (format for readability)
- Can reference files: `#file:../packages/foo.js`

### Roo Code: Mode-Based Profiles

Roo Code takes a different approach using **mode-based configuration profiles** rather than simple rules files.[^26][^27][^28][^29]

**Configuration structure:**

```
~/.roo-code/
└── agents/
    ├── python-developer.yaml
    ├── typescript-developer.yaml
    └── custom-modes/
        └── security-auditor.yaml
```

**Six mode types:**[^27][^29]

1. **Code Mode** - general-purpose coding
2. **Architect Mode** - planning and technical design
3. **Ask Mode** - questions and information
4. **Debug Mode** - systematic problem diagnosis
5. **Orchestrator Mode** - complex task management
6. **Custom Modes** - unlimited specialized personas

**Configuration profiles** configure LLM settings per mode:[^30][^27]

- Model selection (Claude, GPT, Gemini, DeepSeek, local models)
- Temperature settings
- Rate limits
- Reasoning/thinking token limits

**Three interaction modes:**[^27]

- **Manual** - approve every action
- **Hybrid** - auto-approve safe actions, ask for risky ones
- **Auto** - hands-off execution

**Best practices:**

- Create mode-specific configuration profiles
- Match model to mode purpose (e.g., o3-mini for Architect, Sonnet 4 for Code)
- Use custom modes for specialized tasks (security audits, performance optimization)
- Configure auto-approval settings based on trust level
- Use MCP servers for extended functionality
- Set up API keys per provider

### Aider: Convention-Focused CLI Tool

**Configuration methods:**[^31][^32]

**Command line flags:**

```bash
aider --read AGENTS.md main.py
aider --conventions-file CONVENTIONS.md
```

**Config file** (`.aider.conf.yml`):

```yaml
read: AGENTS.md
# or
read: CONVENTIONS.md
```

**File format** - pure markdown, natural language:[^32]

```markdown
# Coding Conventions

## General Rules
- Use httpx instead of requests
- Always include type hints
- Write docstrings for public functions

## Testing
- Use pytest for all tests
- Aim for >80% coverage
- Mock external dependencies
```

**Aider workflow commands:**[^33]

- `/ask` - send instructions without editing (read-only mode)
- `/add` - add files to editing context
- `/read-only` - add files as read-only context
- `/architect` - propose strategy before implementation
- `/code` - implementation mode only

**Best practices:**

- Start with `/ask` mode to set full context
- Use `/add` to specify editable files (prevents unwanted edits)
- Use `/architect` for complex multi-step changes
- Name file AGENTS.md for interoperability with other tools
- Focus on patterns and conventions, not implementation details
- Configure in `.aider.conf.yml` for automatic loading

### Zed: JSON Profile-Based Configuration

Zed uses **JSON-based configuration** in `settings.json` rather than markdown files.[^34][^35][^36]

**Configuration location:**
Access via `zed: open keymap` command

**Profile system example:**[^35]

```json
{
  "agent": {
    "default_model": {
      "provider": "anthropic",
      "model": "claude-sonnet-4"
    },
    "profiles": {
      "write": {
        "tools": ["edit_file", "run_command", "search_codebase"]
      },
      "ask": {
        "tools": ["search_codebase", "read_file"]
      },
      "minimal": {
        "tools": []
      },
      "custom-debug": {
        "tools": ["search_codebase", "read_file", "run_command"]
      }
    },
    "inline_assistant_model": {
      "provider": "openai",
      "model": "gpt-4o"
    }
  }
}
```

**Three built-in profiles:**[^34]

1. **Write** - all tools enabled (edit files, run commands, search)
2. **Ask** - read-only tools (search, read files)
3. **Minimal** - no tools (general conversation)

**Best practices:**

- Create custom profiles for specific workflows
- Use Write profile for agentic file editing
- Use Ask profile for code exploration without changes
- Configure different models per feature (inline assist, commit messages, summaries)
- Use MCP servers to extend tool capabilities
- Enable follow mode to track agent's codebase navigation
- Can run external agents (Claude Code, Gemini CLI) via Zed's agent panel

### Google Jules: GitHub-Integrated Automation

Jules takes a unique approach as an **asynchronous agent** that integrates directly with GitHub and automatically reads AGENTS.md.[^37][^38][^39][^40]

**Configuration approach:**

- Integrates with GitHub repositories
- Clones repo to secure Google Cloud VM
- Reads AGENTS.md and project context automatically
- Operates asynchronously in background
- Uses Gemini 2.5 Pro for coding reasoning

**Workflow:**[^38][^37]

1. Assign GitHub issues to Jules
2. Jules reads AGENTS.md for project context
3. Creates detailed plan with reasoning
4. Executes changes in isolated environment
5. Shows diff and creates pull request
6. Provides audio changelog summaries

**Best practices:**

- Use AGENTS.md in repository root (Jules reads automatically)
- Provide clear build and test commands
- Document project structure thoroughly
- Include API documentation references
- Specify coding standards and patterns clearly
- Jules is asynchronous - can work on multiple tasks concurrently
- Free during beta phase (5 tasks/day limit)

## GitHub Copilot's Ancestor: OpenAI Codex

Although OpenAI Codex is largely superseded by newer agents, many concepts remain relevant for similar asynchronous coding bots.

### Configuration Approach

- **AGENTS.md First**: Codex reads `AGENTS.md` in the repository root by default—no additional config file is required.
- **Task Prompts via Issues**: Operates by assigning GitHub issues to Codex, which then performs changes and submits pull requests.

### Best Practices for Codex

1. **Issue Templates**
    - Include a clear description of the requested change, expected behavior, and links to relevant files.
    - Reference specific AGENTS.md rules in the issue body (e.g., "Follow the Do's under `Commands` in AGENTS.md").
2. **Branch Isolation**
    - Always create a new branch per Codex task (e.g., `codex/feature-x`).
    - Instruct Codex via the issue to create and commit in the task-specific branch.
3. **Automated Testing in CI**
    - Ensure CI runs lint, type checks, and tests on Codex's branches before merging.
    - Include commands from AGENTS.md in CI config (e.g., `npm run tsc --noEmit`, `npm run vitest`).
4. **Pull Request Templates**
    - Use PR templates that reference AGENTS.md and require confirmation that rules were followed.
    - Automate a checklist in the PR description: "✅ Lint/Type checks passed," "✅ Tests covering new code," "✅ AGENTS.md rules applied."
5. **Rate Limits & API Keys**
    - Use dedicated API keys for Codex with strict usage quotas.
    - Rotate keys regularly and scope them to read/write only code repositories.
6. **Prompt Engineering**
    - Begin prompts with a concise summary: "Codex, generate a data-fetching hook following the rules in AGENTS.md."
    - End prompts with explicit permission: "You may commit changes in branch `codex/feature-x` once tests pass."
7. **Review & Oversight**
    - Always perform a human code review before merging Codex-generated PRs.
    - Confirm that Codex did not introduce security or license violations.

## OpenCode-Specific Configuration

OpenCode (e.g., Sourcegraph Amp) integrates deeply into editors and CI pipelines.

### Configuration Approach

- **AGENTS.md Compatible**: Reads `AGENTS.md` natively at project root or nearest directory.
- **Workspace Settings**: Uses editor settings (VS Code, JetBrains) to enable or disable agentic features.
- **CI Integration**: Can run `open-code analyze` in CI for automated code checks against AGENTS.md.

### File Structure & Settings

```
project-root/
├── AGENTS.md
├── open-code.config.json       # Optional overrides
└── .github/
    └── workflows/
        └── open-code-ci.yml    # CI workflow invoking OpenCode checks
```

#### Example `open-code.config.json`

```json
{
  "rulesFile": "AGENTS.md",
  "enableLintChecks": true,
  "enableTestChecks": true,
  "allowedCommands": [
    "tsc --noEmit",
    "eslint --fix",
    "vitest run"
  ],
  "denyCommands": [
    "npm run build",
    "git push --force"
  ],
  "maxFileSizeKB": 200
}
```

### Best Practices for OpenCode

1. **Centralize in AGENTS.md**
    - Define all conventions there; use `open-code.config.json` only for overrides (e.g., disabling specific rules temporarily).
2. **Strict CI Enforcement**
    - In `open-code-ci.yml`, add a job:

```yaml
jobs:
  open-code-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run OpenCode Checks
        run: open-code analyze --config open-code.config.json
```

    - Fail CI if any AGENTS.md rule is violated.
3. **Editor Extensions**
    - Install the OpenCode extension in VS Code or JetBrains.
    - Configure "Live Analysis" to highlight AGENTS.md violations in real time.
4. **Incremental Adoption**
    - Start with `pre-commit` hooks that run `open-code analyze --staged`.
    - Gradually enforce on all code by adding CI checks.
5. **Granular Overrides**
    - Use `open-code.config.json` to whitelist or blacklist specific rules per directory.
    - Example:

```json
{
  "overrides": [
    {
      "path": "legacy/",
      "disableRules": ["no-legacy-patterns"]
    }
  ]
}
```

6. **Performance Tuning**
    - Set `maxFileSizeKB` to limit analysis on large auto-generated files.
    - Cache linter and type-checker artifacts between CI runs.
7. **Documentation & Onboarding**
    - Include a section in `README.md` under "AI Agent Configuration" describing how to install and use the OpenCode extension and CI job.
    - Provide sample workflows: "How to fix an AGENTS.md violation in VS Code."

## Common Configuration Patterns

### The "Do's and Don'ts" Pattern

**Most effective when specific:**[^6][^1]

✅ **Good:**

```markdown
## Do's
- Use MUI v3 (ensure v3 compatibility)
- Use emotion css={{}} prop format
- Use mobx with useLocalStore for state
- Keep components under 200 lines

## Don'ts
- Do not hardcode colors (use tokens)
- Do not add divs if component exists
- Do not add heavy dependencies without approval
- Do not use class components (use functional)
```

❌ **Avoid vague guidance:**

```markdown
## Do's
- Write clean code
- Follow best practices
- Be consistent
```

### The "Commands" Pattern

**Specify file-scoped over project-wide:**[^1][^6]

✅ **Good:**

```markdown
## Commands
# File-scoped checks (preferred - fast feedback)
npm run tsc --noEmit path/to/file.tsx
npm run prettier --write path/to/file.tsx
npm run eslint --fix path/to/file.tsx

# Tests
npm run vitest run path/to/file.test.tsx

# Full build (only when explicitly requested)
npm run build
```

❌ **Avoid:**

```markdown
## Commands
npm run build  # Always run full build (slow!)
```

### The "Good/Bad Examples" Pattern

**Point to actual files:**[^6][^1]

✅ **Good:**

```markdown
## Good and Bad Examples
- ✅ Functional components: components/Projects.tsx
- ❌ Class components: components/Admin.tsx (legacy, avoid)
- ✅ Forms: copy app/components/Form.Field.tsx pattern
- ✅ Charts: copy app/components/Charts/Bar.tsx
- ✅ Data layer: use app/api/client.ts, not direct fetch
```

❌ **Avoid abstract guidance:**

```markdown
## Examples
- Use good patterns
- Avoid bad patterns
```

### The "Safety & Permissions" Pattern

**Be explicit about boundaries:**[^1][^6]

✅ **Good:**

```markdown
## Safety and Permissions

Allowed without prompt:
- Read files, list files
- Run file-scoped linters/formatters
- Run single test files
- Search codebase

Ask first:
- Package installs
- Git push/force push
- Deleting files or directories
- chmod/permission changes
- Full builds or E2E test suites
```

## Multi-Tool Configuration Strategy

**Best practice:** Use AGENTS.md as single source of truth, symlink tool-specific files:

```
project-root/
├── AGENTS.md                           # Primary configuration
├── .cursorrules -> AGENTS.md           # Symlink for Cursor
├── .clinerules -> AGENTS.md            # Symlink for Cline
├── .windsurfrules -> AGENTS.md         # Symlink for Windsurf
├── CLAUDE.md -> AGENTS.md              # Symlink for Claude Code
└── .github/
    └── copilot-instructions.md -> ../../AGENTS.md  # Symlink for Copilot
```

**Or use reference files** for tools without symlink support:

```markdown
# CLAUDE.md
Strictly follow the rules in ./AGENTS.md
```

## Testing Your Configuration

**5-step verification process:**

1. **File detection** - verify agent recognizes configuration file
2. **Simple prompts** - test code generation follows your rules
3. **Edge cases** - request deprecated functionality (should refuse)
4. **Consistency** - generate same component multiple times, check patterns
5. **Iterate** - add rules when mistakes occur, update examples as project evolves

## Maintenance Best Practices

**Regular maintenance schedule:**

**Monthly:**

- Review rules for outdated patterns
- Update framework/library versions
- Add conventions from recent code reviews
- Remove deprecated rules

**Per major change:**

- Update when upgrading dependencies
- Revise when changing architecture
- Adjust when adopting new patterns

**After issues:**

- Add rule when agent makes same mistake twice
- Clarify ambiguous rules causing confusion

**Version control:**

```gitignore
# .gitignore - keep configs in repo
!AGENTS.md
!.cursor/rules/
!.clinerules
!.windsurfrules
!.github/copilot-instructions.md
```

## Key Takeaways

1. **Start with AGENTS.md** - the universal standard adopted by 20,000+ projects
2. **Be specific** - "Use MUI v3" beats "write good code"
3. **Use concrete examples** - point to real files in your codebase
4. **Prefer file-scoped commands** - faster feedback than full builds
5. **Define permission boundaries** - what's auto-approved vs. ask-first
6. **Iterate continuously** - add rules as patterns emerge
7. **Use symlinks** - one AGENTS.md, multiple tool-specific references
8. **Keep focused** - split large configs into multiple files
9. **Version control** - track configs in git for team consistency
10. **Test regularly** - verify rules actually influence agent behavior

The vibe coding ecosystem is converging on common patterns while maintaining tool-specific strengths. By following these best practices and adopting AGENTS.md as your foundation, you'll create a maintainable, portable configuration that works across the entire ecosystem.

By extending universal AGENTS.md principles with Codex's issue-driven workflow and OpenCode's editor/CI integration, you ensure consistency, speed, and maintainability across all AI-assisted coding tools.

<div align="center">⁂</div>

[^1]: https://www.builder.io/blog/agents-md

[^2]: https://agents.md

[^3]: https://www.infoq.com/news/2025/08/agents-md/

[^4]: https://github.com/openai/agents.md

[^5]: https://github.com/agentmd/agent.md

[^6]: https://trigger.dev/blog/cursor-rules

[^7]: https://staging-real-3.windsurf.com/university/general-education/creating-modifying-rules

[^8]: https://docs.windsurf.com/windsurf/cascade/memories

[^9]: https://playbooks.com/rules/create-rules

[^10]: https://docs.cline.bot/features/cline-rules

[^11]: https://publish.obsidian.md/aixplore/AI+Development+%26+Agents/mastering-clinerules-configuration

[^12]: https://forum.cursor.com/t/optimal-structure-for-mdc-rules-files/52260

[^13]: https://leostudyai.top/posts/cm9l96mvz0001l4043894vuvk

[^14]: https://www.linkedin.com/posts/clinebot_new-in-cline-370-the-clinerules-folder-activity-7307544147354099713-iKkt

[^15]: https://cline.bot/blog/clinerules-version-controlled-shareable-and-ai-editable-instructions

[^16]: https://aise.chat/en/docs/products/clinepro/prompting/readme/

[^17]: https://www.reddit.com/r/Codeium/comments/1hw6hcz/how_to_write_windsurf_rules_files_for_cascade/

[^18]: https://github.com/kinopeee/windsurfrules

[^19]: https://coding-cloud.com/snippets/windsurf-rules-file

[^20]: https://www.youtube.com/watch?v=D3uuCeOLfJA

[^21]: https://www.reddit.com/r/windsurf/comments/1kid3a3/memories_activation_modes_how_do_we_usedo_it_wave/

[^22]: https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot

[^23]: https://docs.github.com/fr/enterprise-cloud@latest/copilot/how-tos/configure-custom-instructions/add-repository-instructions?tool=vscode

[^24]: https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions?tool=vscode

[^25]: https://github.blog/changelog/2025-09-03-copilot-code-review-path-scoped-custom-instruction-file-support/

[^26]: https://github.com/jtgsystems/Custom-Modes-Roo-Code

[^27]: https://www.datacamp.com/tutorial/roo-code

[^28]: https://www.linkedin.com/pulse/my-settings-agentic-coding-roo-code-reuven-cohen-0l44c

[^29]: https://docs.roocode.com

[^30]: https://www.youtube.com/watch?v=eEJErgZBqLE

[^31]: https://github.com/aider-ai/aider/issues/4363

[^32]: https://aider.chat/docs/usage/conventions.html

[^33]: https://zenn.dev/takets/articles/how-to-use-aider-en

[^34]: https://zed.dev/docs/ai/agent-panel

[^35]: https://zed.dev/docs/ai/agent-settings

[^36]: https://zed.dev/agentic

[^37]: https://blog.google/technology/google-labs/jules/

[^38]: https://www.datacamp.com/tutorial/google-jules

[^39]: https://www.reddit.com/r/GoogleGeminiAI/comments/1kqr0am/googles_answer_to_codex_is_here_meet_jules/

[^40]: https://jules.google

[^41]: https://github.com/anthropics/claude-code/issues/6235

[^42]: https://retool.com/blog/build-agent-with-prompts

[^43]: https://cline.bot/blog/how-to-use-cline-from-cursor-or-windsurf

[^44]: https://www.reddit.com/r/ClaudeAI/comments/1ngu3og/how_to_make_claude_understand_the_agentsmd_and/

[^45]: https://github.com/iNeuronix-ai/Build-Your-Own-Vibe-Coding-Agent-The-Future-of-AI-Powered-Development

[^46]: https://uibakery.io/blog/cursor-vs-windsurf-vs-cline

[^47]: https://www.linkedin.com/posts/riley-brown-7441b1101_you-can-build-apps-with-ai-agents-and-complex-activity-7344508769336270848-c_aB

[^48]: https://www.llmwatch.com/p/dont-believe-the-vibe-best-practices

[^49]: https://arize.com/blog/optimizing-coding-agent-rules-claude-md-agents-md-clinerules-cursor-rules-for-improved-accuracy/

[^50]: https://www.llamaindex.ai/blog/the-future-of-vibe-coding-agents

[^51]: https://www.builder.io/blog/windsurf-vs-cursor

[^52]: https://www.youtube.com/watch?v=0c_NpEBLltM

[^53]: https://www.eesel.ai/blog/cursor-vs-windsurf

[^54]: https://pnote.eu/notes/agents-md/

[^55]: https://www.stephenwthomas.com/azure-integration-thoughts/vibe-coding-build-a-c-api-app-with-ai-agents-in-vs-code-no-coding-needed/

[^56]: https://www.reddit.com/r/ChatGPTCoding/comments/1ioojwq/for_those_with_experience_cursor_windsurf_or/

[^57]: https://blog.kilocode.ai/p/agentsmd-may-trick-us-into-writing

[^58]: https://docs.replit.com/tutorials/vibe-coding-101

[^59]: https://www.dynatrace.com/news/blog/mcp-best-practices-cline-live-debugger-developer-experience/

[^60]: https://www.datacamp.com/tutorial/cline-ai

[^61]: https://devcenter.upsun.com/posts/why-your-readme-matters-more-than-ai-configuration-files/

[^62]: https://www.eesel.ai/blog/skills-md-vs-agents-md

[^63]: https://forum.cursor.com/t/cursor-rules-files-format-arent-clear/51419

[^64]: https://www.reddit.com/r/CLine/comments/1kajzje/cline_best_practices/

[^65]: https://www.reddit.com/r/cursor/comments/1iq2grw/how_do_you_structure_your_cursorrules/

[^66]: https://docs.cline.bot

[^67]: https://gist.github.com/0xdevalias/f40bc5a6f84c4c5ad862e314894b2fa6

[^68]: https://workweave.dev/blog/what-to-put-in-my-teams-cursor-rules-file

[^69]: https://cline.bot

[^70]: https://github.com/PatrickJS/awesome-cursorrules

[^71]: https://www.reddit.com/r/GithubCopilot/comments/1llss4p/this_is_my_generalinstructionsmd_file_to_use_with/

[^72]: https://copilot-instructions.md

[^73]: https://linux.do/t/topic/403099

[^74]: https://blog.stephane-robert.info/docs/developper/autres-outils/ide/visual-studio-code/copilot-instructions-prompts/

[^75]: https://github.com/kinopeee/windsurfrules/blob/main/README.md

[^76]: https://code.visualstudio.com/docs/copilot/customization/custom-instructions

[^77]: https://roocode.com

[^78]: https://github.blog/changelog/2025-07-23-github-copilot-coding-agent-now-supports-instructions-md-custom-instructions/

[^79]: https://windsurf.com/editor/directory

[^80]: https://www.youtube.com/watch?v=pOvI02of5oo

[^81]: https://www.youtube.com/watch?v=fjIioQ4R1r8

[^82]: https://docs.windsurf.com/windsurf/cascade/cascade

[^83]: https://apidog.com/blog/deepseek-r1-roocode-ai/

[^84]: https://www.eesel.ai/blog/windsurf-overview

[^85]: https://www.youtube.com/watch?v=S6nEm6VZCH0

[^86]: https://www.youtube.com/watch?v=7PMXUruAIdQ

[^87]: https://www.blogdumoderateur.com/comment-tester-jules-agent-ia-google-code/

[^88]: https://aider.chat

[^89]: https://www.reddit.com/r/ZedEditor/comments/1kuz47h/agent_mode_with_local_llms_anyone_got_this/

[^90]: https://www.youtube.com/watch?v=ZmDtilWdiDg

[^91]: https://docs.replit.com/tutorials/vibe-coding-101

[^92]: https://martinfowler.com/articles/exploring-gen-ai/autonomous-agents-codex-example.html

[^93]: https://aider.chat/docs/install.html

[^94]: https://github.com/vallades/awesome-windsurfrules/blob/main/.windsurfrules

[^95]: https://copilotthatjawn.com/tips/copilot-instructions-md.md

[^96]: https://www.reddit.com/r/ChatGPTCoding/comments/1jl6gll/copilotinstructionsmd_has_helped_me_so_much/

[^97]: https://blog.nashtechglobal.com/tips-for-using-github-copilot-instruction-files-or-agents-md-effectively/

[^98]: https://www.reddit.com/r/CLine/comments/1ke29a4/format_for_cline_rules_files/

[^99]: https://www.reddit.com/r/Codeium/comments/1gsl9cv/rules_for_the_ai_in_windsurf_like_the_cursorrules/