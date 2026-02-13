# Getting the Best Out of GitHub Copilot

A practical guide to configuring GitHub Copilot for maximum accuracy and usefulness using instruction files, MCP servers, prompt files, chat modes, agentic memory, and project-level setup patterns.

---

## 1. Instruction Files — The Foundation

Instruction files are the single most impactful way to improve Copilot's output. They provide persistent context about your project, coding standards, and preferences.

### Repository-Wide Instructions

Create `.github/copilot-instructions.md` at the root of your repo. This file is automatically included in every Copilot Chat request within that workspace.

```markdown
# Project: MyApp API

## Tech Stack
- Python 3.12, FastAPI, SQLAlchemy 2.0, Alembic
- PostgreSQL 16, Redis for caching
- pytest for testing, Ruff for linting

## Coding Standards
- Use type hints on all function signatures
- Follow Google-style docstrings
- Use early returns for error conditions
- All API endpoints must include input validation via Pydantic models
- Never use `print()` — use structured logging via `structlog`

## Build & Test Commands
- `make dev` — start local dev server
- `make test` — run full test suite
- `make lint` — run Ruff linter
```

**Quick start:** Type `/init` in VS Code chat to auto-generate instructions from your codebase.

**Or let Copilot generate it:** In the Copilot agents panel on GitHub.com, paste the prompt from GitHub Docs to have the coding agent analyze your repo and create the file via PR.

### Path-Specific Instructions

For rules that apply only to certain file types, create `*.instructions.md` files in `.github/instructions/`:

```markdown
<!-- .github/instructions/react-components.instructions.md -->
---
applyTo: "src/components/**/*.tsx"
---
- Use functional components with hooks only
- All props must have TypeScript interfaces (not `type`)
- Use TanStack Query for data fetching, never useEffect for API calls
- Wrap components in React.memo() if they accept callback props
```

This is powerful for monorepos or projects mixing languages/frameworks — you get different rules for backend vs. frontend, tests vs. production code, etc.

### Best Practices for Instructions

- Keep any single file under **~1,000 lines** — beyond this, quality drops
- Be specific and concrete, not vague ("`Always use Bazel for Java`" > "`Use our build tool`")
- Include exact commands that work, and note any that don't
- Document known workarounds for common errors
- Update iteratively — start small, add rules when Copilot makes repeated mistakes

---

## 2. AGENTS.md — Cross-Tool Agent Instructions

`AGENTS.md` is an emerging standard (supported by VS Code, Copilot, Cursor, Claude Code, and others) that serves as machine-readable instructions for AI coding agents.

```markdown
# AGENTS.md

## Project Overview
E-commerce API built with FastAPI. Hexagonal architecture with ports/adapters pattern.

## Architecture Rules
- All business logic lives in `src/domain/` — never import framework code here
- Database access only through repository interfaces in `src/ports/`
- API routes are thin wrappers that call domain services

## Testing
- Never create throwaway test scripts — all tests go in `tests/`
- Run tests with: `pytest --cov=src tests/`
- Minimum 80% coverage required for PRs

## Common Mistakes to Avoid
- Don't use raw SQL — always use SQLAlchemy ORM
- Don't commit `.env` files
- Don't add dependencies without updating `pyproject.toml`
```

**Key difference from `copilot-instructions.md`:** AGENTS.md is portable across multiple AI tools and explicitly curated by you. It's version-controlled and intentional. If you already have `.github/copilot-instructions.md`, both files will be read — use AGENTS.md for cross-tool compatibility.

---

## 3. MCP (Model Context Protocol) Configuration

MCP extends Copilot by connecting it to external tools and services — databases, APIs, issue trackers, cloud platforms, and more.

### VS Code Setup (`.vscode/mcp.json`)

```json
{
  "servers": {
    "github": {
      "url": "https://copilot-mcp.githubusercontent.com/v1",
      "type": "http"
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost:5432/mydb"],
      "type": "stdio"
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/dir"],
      "type": "stdio"
    }
  }
}
```

### Coding Agent MCP (Repository Settings on GitHub.com)

For the Copilot coding agent, configure MCP in **Repo Settings → Copilot → Coding Agent**:

```json
{
  "mcpServers": {
    "sentry": {
      "type": "http",
      "url": "https://mcp.sentry.dev/sse",
      "headers": {
        "Authorization": "Bearer $COPILOT_MCP_SENTRY_TOKEN"
      }
    }
  }
}
```

- Secrets must be prefixed with `COPILOT_MCP_` and configured in your Copilot environment
- Use the `tools` array to limit which tools from an MCP server are enabled
- The GitHub MCP server and Playwright MCP server are enabled by default for the coding agent

### Guide Tool Selection via Instructions

Add rules in your `copilot-instructions.md` to guide how Copilot uses MCP tools:

```markdown
- When querying the database, use the read-only MCP tool instead of direct SQL
- Prefer the GitHub MCP server for all repository operations
- Use the Playwright MCP for browser testing tasks
```

---

## 4. Prompt Files — Reusable Task Templates

Prompt files (`.prompt.md`) define reusable slash commands for common tasks. Place them in `.github/prompts/`.

```markdown
<!-- .github/prompts/create-api-endpoint.prompt.md -->
---
description: "Scaffold a new FastAPI endpoint with tests"
mode: "agent"
tools: ["editFiles", "runCommands", "codebase"]
---
Create a new API endpoint following our project conventions:
1. Add the route in `src/api/routes/`
2. Create a Pydantic request/response model in `src/api/schemas/`
3. Add business logic in `src/domain/services/`
4. Write tests in `tests/api/` with at least 3 test cases
5. Run `make lint` and `make test` to verify
```

Invoke it in chat with `/create-api-endpoint`. In Visual Studio, use `#prompt:create-api-endpoint`.

---

## 5. Custom Chat Modes

Chat modes define how Copilot behaves for specific workflows. Create `.chatmode.md` files in `.github/chatmodes/`:

```markdown
<!-- .github/chatmodes/code-reviewer.chatmode.md -->
---
description: "Senior code reviewer focused on security and performance"
tools: ["codebase", "githubRepo"]
---
You are a senior code reviewer. For every code change:
- Check for SQL injection, XSS, and authentication bypasses
- Flag N+1 query patterns and unnecessary allocations
- Verify error handling covers edge cases
- Ensure new code has corresponding tests
- Be concise — flag issues with file:line references
```

Built-in modes: **Ask** (conversational), **Edit** (controlled file changes), **Agent** (autonomous multi-step). Custom modes let you create specialized workflows like "DBA mode", "Security Auditor", or "Documentation Writer".

---

## 6. Agentic Memory (Public Preview)

Copilot can now build persistent, repository-scoped memories that improve over time.

**What it does:** As Copilot works in your repo (via coding agent, code review, or CLI), it automatically discovers and stores patterns — like how your repo handles database connections, which files must stay synchronized, or which testing patterns your team follows.

**Key characteristics:**
- Memories are scoped to individual repositories (not shared across repos)
- Each memory includes citations to specific code locations
- Memories are validated against current codebase before use — stale info is ignored
- Auto-deleted after 28 days unless refreshed
- Used by: Coding Agent, Code Review, and Copilot CLI

**Enable it:** GitHub Settings → Copilot → Features → Copilot Memory → Enabled

**Manage memories:** Repo Settings → Copilot → Memory — view, review, and delete stored memories.

This complements (not replaces) instruction files. Instructions are explicit and curated by you; memories are implicit and learned by Copilot.

---

## 7. Context Engineering — Feeding Copilot the Right Information

The quality of Copilot's output is directly proportional to the context you provide.

### In the Editor
- **Open relevant files** — Copilot reads neighboring tabs for definitions, patterns, and usage
- **Close irrelevant files** — reduces noise in Copilot's context window
- **Set up imports first** — write `import pandas as pd` before asking Copilot to manipulate DataFrames
- **Use descriptive names** — `calculate_invoice_total()` gives Copilot far more signal than `foo()`

### In Chat
- `#file:path/to/file.py` — reference specific files
- `#codebase` — search the full workspace for relevant code
- `#selection` — reference highlighted code
- `#fetch:URL` — pull content from a web page
- `#githubRepo` — search a GitHub repository
- `@workspace` — broad workspace context
- `@terminal` — terminal/CLI context

### Session Hygiene
- Start new chat sessions for unrelated tasks — stale context degrades quality
- Use `/clear` or `/new` between tasks in Copilot CLI
- Use **subagents** for isolated research that shouldn't pollute your main context
- Use **Plan mode** (Shift+Tab in CLI) to create a structured plan before implementation

---

## 8. Agent Skills

Agent Skills are self-contained folders with instructions and bundled resources for specialized tasks. They are loaded on-demand based on the task.

```
.github/skills/
  database-migration/
    SKILL.md          # Instructions for the skill
    templates/        # Reference files and scripts
    examples/         # Example migrations
```

The `SKILL.md` YAML frontmatter should include a clear description of **what the skill does and when to use it** — Copilot uses this to decide whether to load the skill.

Skills work across VS Code, Copilot CLI, and the coding agent.

---

## 9. Hooks — Event-Driven Automation

Hooks trigger automated workflows during Copilot coding agent sessions:

- `sessionStart` — run setup tasks when a session begins
- `sessionEnd` — cleanup, auto-commit, or log results
- `userPromptSubmitted` — intercept and augment prompts before processing

Use cases: auto-formatting on save, logging agent actions, integrating with external CI/CD, or enforcing pre-commit checks.

---

## 10. Recommended Project Structure

```
your-repo/
├── .github/
│   ├── copilot-instructions.md    # Repository-wide instructions
│   ├── instructions/
│   │   ├── python.instructions.md  # Python-specific rules
│   │   ├── react.instructions.md   # React-specific rules
│   │   └── tests.instructions.md   # Testing conventions
│   ├── prompts/
│   │   ├── new-feature.prompt.md
│   │   └── code-review.prompt.md
│   ├── chatmodes/
│   │   └── architect.chatmode.md
│   └── skills/
│       └── db-migration/
│           └── SKILL.md
├── .vscode/
│   └── mcp.json                    # MCP server configuration
├── AGENTS.md                       # Cross-tool agent instructions
└── ...
```

---

## Quick Reference Cheat Sheet

| Feature | Location | Scope | Use For |
|---|---|---|---|
| `copilot-instructions.md` | `.github/` | All chat requests in repo | Coding standards, tech stack, build commands |
| `*.instructions.md` | `.github/instructions/` | Matched files (via `applyTo` glob) | Language/framework-specific rules |
| `AGENTS.md` | Repo root | All AI agents (cross-tool) | Architecture, testing, common mistakes |
| `mcp.json` | `.vscode/` | Workspace | External tool integrations |
| `*.prompt.md` | `.github/prompts/` | On-demand (slash commands) | Reusable task templates |
| `*.chatmode.md` | `.github/chatmodes/` | Chat session | Specialized interaction modes |
| Agent Skills | `.github/skills/` | On-demand | Complex specialized tasks |
| Copilot Memory | GitHub Settings | Per-repository (auto) | Learned patterns over time |

---

## Key Resources

- [GitHub Copilot Docs — Custom Instructions](https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot)
- [VS Code Copilot Customization](https://code.visualstudio.com/docs/copilot/customization/overview)
- [Awesome GitHub Copilot Repo](https://github.com/github/awesome-copilot) — community-contributed instructions, prompts, and chat modes
- [MCP Server Setup Guide](https://docs.github.com/copilot/how-tos/provide-context/use-mcp/set-up-the-github-mcp-server)
- [Copilot Coding Agent Best Practices](https://docs.github.com/copilot/how-tos/agents/copilot-coding-agent/best-practices-for-using-copilot-to-work-on-tasks)
- [Agentic Memory Documentation](https://docs.github.com/copilot/concepts/agents/copilot-memory)
- [Copilot CLI Best Practices](https://docs.github.com/copilot/how-tos/copilot-cli/cli-best-practices)
