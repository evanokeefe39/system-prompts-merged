<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Codex and OpenCode–Specific Best Practices

## GitHub Copilot’s Ancestor: OpenAI Codex

Although OpenAI Codex is largely superseded by newer agents, many concepts remain relevant for similar asynchronous coding bots.

### Configuration Approach

- **AGENTS.md First**: Codex reads `AGENTS.md` in the repository root by default—no additional config file is required.
- **Task Prompts via Issues**: Operates by assigning GitHub issues to Codex, which then performs changes and submits pull requests.


### Best Practices for Codex

1. **Issue Templates**
    - Include a clear description of the requested change, expected behavior, and links to relevant files.
    - Reference specific AGENTS.md rules in the issue body (e.g., “Follow the Do’s under `Commands` in AGENTS.md”).
2. **Branch Isolation**
    - Always create a new branch per Codex task (e.g., `codex/feature-x`).
    - Instruct Codex via the issue to create and commit in the task-specific branch.
3. **Automated Testing in CI**
    - Ensure CI runs lint, type checks, and tests on Codex’s branches before merging.
    - Include commands from AGENTS.md in CI config (e.g., `npm run tsc --noEmit`, `npm run vitest`).
4. **Pull Request Templates**
    - Use PR templates that reference AGENTS.md and require confirmation that rules were followed.
    - Automate a checklist in the PR description: “✅ Lint/Type checks passed,” “✅ Tests covering new code,” “✅ AGENTS.md rules applied.”
5. **Rate Limits \& API Keys**
    - Use dedicated API keys for Codex with strict usage quotas.
    - Rotate keys regularly and scope them to read/write only code repositories.
6. **Prompt Engineering**
    - Begin prompts with a concise summary: “Codex, generate a data-fetching hook following the rules in AGENTS.md.”
    - End prompts with explicit permission: “You may commit changes in branch `codex/feature-x` once tests pass.”
7. **Review \& Oversight**
    - Always perform a human code review before merging Codex–generated PRs.
    - Confirm that Codex did not introduce security or license violations.

***

## OpenCode–Specific Configuration

OpenCode (e.g., Sourcegraph Amp) integrates deeply into editors and CI pipelines.

### Configuration Approach

- **AGENTS.md Compatible**: Reads `AGENTS.md` natively at project root or nearest directory.
- **Workspace Settings**: Uses editor settings (VS Code, JetBrains) to enable or disable agentic features.
- **CI Integration**: Can run `open-code analyze` in CI for automated code checks against AGENTS.md.


### File Structure \& Settings

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
    - Configure “Live Analysis” to highlight AGENTS.md violations in real time.
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
7. **Documentation \& Onboarding**
    - Include a section in `README.md` under “AI Agent Configuration” describing how to install and use the OpenCode extension and CI job.
    - Provide sample workflows: “How to fix an AGENTS.md violation in VS Code.”

***

By extending universal AGENTS.md principles with Codex’s issue-driven workflow and OpenCode’s editor/CI integration, you ensure consistency, speed, and maintainability across all AI-assisted coding tools.

