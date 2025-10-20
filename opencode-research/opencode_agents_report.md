# Comprehensive Report: Implementing Agents in OpenCode

## Executive Summary
This report synthesizes best practices for implementing agents in OpenCode, drawing from official documentation. Agents are specialized AI assistants configurable for specific tasks, enhancing productivity while maintaining security. Key recommendations include starting with built-in agents, using Plan mode for complex changes, and leveraging permissions for safety.

## Agent Types and Architecture
### Primary Agents
- **Build**: Default full-access agent for development tasks. Enables all tools (write, edit, bash).
- **Plan**: Restricted agent for analysis and planning. Permissions default to "ask" for edits/bash, preventing unintended changes.
- **Custom Primary**: User-defined agents for main interactions, cycled via Tab key.

### Subagents
- Specialized helpers invoked by primary agents or manually via `@mention` (e.g., `@general`).
- Ideal for delegation without polluting main context.
- Built-in: **General** for searches and multi-step tasks.

### Configuration Formats
- **JSON**: In `opencode.json` for structured config.
- **Markdown**: In `.opencode/agent/` for frontmatter-based setup.
- **Global vs. Project**: Global in `~/.config/opencode/` for personal use; project-specific for team sharing.

## Best Practices for Implementation
### Design Principles
1. **Purpose-Driven**: Define clear roles (e.g., one for planning, one for execution).
2. **Security-First**: Start with restrictive permissions ("ask" for dangerous ops).
3. **Modular**: Use subagents for specialized tasks to maintain context clarity.
4. **Version Control**: Commit agent configs to Git for team consistency.

### Configuration Best Practices
- **Description**: Always include a clear, concise description for invocation guidance.
- **Model Selection**: Match to task (e.g., fast models for planning, capable ones for implementation). Use OpenCode Zen for tested models.
- **Temperature**: 0.0-0.2 for deterministic tasks (analysis), 0.6-1.0 for creative (brainstorming).
- **Tools**: Disable unnecessary tools (e.g., no `write` for reviewers). Use wildcards for MCP servers.
- **Permissions**: Layered approach: global defaults, agent overrides. Use globs (e.g., `"git *": "ask"`) and wildcards (`"*": "deny"` except specifics).
- **Prompts**: Concise, specific, with examples. Reference external files via `instructions` in config.

### Workflow Integration
- **Plan Mode**: Use for complex features to review before building.
- **Session Management**: Leverage `/new`, `/sessions`, and child session navigation (Ctrl+Right/Left).
- **Undo/Redo**: Requires Git repo; use for safe experimentation.
- **Sharing**: Use `/share` for collaboration, with links for reference.

## Implementation Methods
### Interactive Creation
- Command: `opencode agent create`.
- Guides through location, description, tools, and prompt generation.
- Generates config automatically.

### Manual Configuration
- **JSON Example**:
  ```json
  {
    "agent": {
      "reviewer": {
        "description": "Code quality reviewer",
        "mode": "subagent",
        "model": "anthropic/claude-haiku-4-20250514",
        "temperature": 0.1,
        "tools": {"write": false},
        "permission": {"edit": "deny"}
      }
    }
  }
  ```
- **Markdown Example**:
  ```
  ---description: Code reviewer
  mode: subagent
  model: anthropic/claude-haiku-4-20250514
  temperature: 0.1
  tools:
    write: false
  ---
  Focus on security and performance.
  ```

### Advanced: SDK and Server
- **SDK Usage**: `npm install @opencode-ai/sdk`; create client for programmatic control.
  - List agents: `client.app.agents()`.
  - Manage sessions: `client.session.create()`, `client.session.prompt()`.
- **Server Mode**: `opencode serve` exposes HTTP APIs (e.g., `/agent` for listing, `/session` for management).
- **OpenAPI Spec**: Available at `/doc` for client generation.

### Plugins for Extension
- **Structure**: Export functions from `.opencode/plugin/`.
- **Custom Tools**: Use `tool()` helper with Zod schemas.
- **Hooks**: Respond to events (e.g., session completion) or tool execution.
- **Example**:
  ```js
  export const CustomAgentPlugin = async ({ client }) => ({
    tool: {
      mytool: tool({
        description: "Custom logic",
        args: { param: tool.schema.string() },
        async execute(args) { return `Result: ${args.param}`; }
      })
    }
  });
  ```

## Common Failure Modes
### Configuration Errors
- **Missing Descriptions**: Leads to poor invocation; always include.
- **Over-Permissive Tools**: Enables unintended changes; start restrictive.
- **Model Mismatches**: Slow models for simple tasks; test performance.

### Runtime Issues
- **Permission Denials**: Excessive "ask" prompts disrupt flow; balance with "allow" for safe commands.
- **Context Pollution**: Subagents not used; main sessions become cluttered.
- **Git Dependency**: Undo/redo fails without repo; ensure initialized.

### Security Risks
- **Tool Abuse**: Unrestricted bash allows harmful commands; use wildcards to deny risky ops.
- **Secret Exposure**: No built-in masking; avoid logging sensitive data.
- **Unverified Models**: Inconsistent output; stick to Zen or tested providers.

### Integration Problems
- **Plugin Conflicts**: Multiple plugins hooking same events; test in isolation.
- **SDK Connection**: Server not running; use `createOpencode()` to start both.
- **Type Errors**: Zod schema mismatches; validate arguments.

## Optimizations
### Performance
- **Model Efficiency**: Use faster models (e.g., Haiku) for planning; reserve Sonnet for complex tasks.
- **Tool Limiting**: Disable unused tools to reduce overhead.
- **Caching**: Leverage session reuse; avoid recreating agents per task.

### Scalability
- **Modular Agents**: Break workflows into subagents for parallel processing.
- **Global Agents**: Reuse across projects for consistency.
- **Automation**: Use SDK for CI/CD integration (e.g., automated reviews).

### User Experience
- **Keybinds**: Customize for quick agent switching.
- **Themes**: Match IDE for seamless integration.
- **Notifications**: Plugin hooks for completion alerts.

### Monitoring
- **Logs**: Enable `--print-logs` for debugging.
- **Metrics**: Track session success via SDK events.
- **Updates**: Run `opencode upgrade` for latest features.

## Advanced Techniques
### Multi-Agent Workflows
- Chain agents: Plan → Build → Review.
- Use `@mention` for manual invocation.
- Session navigation for complex hierarchies.

### Custom Integrations
- **MCP Servers**: Extend tools via Model Context Protocol.
- **LSP Servers**: Enhance code analysis.
- **External Services**: Plugins for notifications or API calls.

### Enterprise Considerations
- **Privacy**: No data storage; suitable for sensitive environments.
- **Team Rules**: Shared AGENTS.md and permissions.
- **Auditing**: Log tool usage via plugins.

## Conclusion
Implementing agents in OpenCode requires balancing flexibility with security. Start with built-in agents and Plan mode, then customize via JSON/Markdown configs. Use permissions restrictively, leverage SDK for automation, and extend via plugins. Common pitfalls include over-permissiveness and model mismatches—mitigate with testing and iteration. Optimizations focus on efficiency and UX. For production, prioritize version control and team alignment. This approach maximizes productivity while minimizing risks. For specific implementations, refer to the docs or provide use case details.