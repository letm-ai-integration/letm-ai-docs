# GitHub Copilot & its modes Guide

## 1. What is GitHub Copilot?

GitHub Copilot is an AI-powered pair programmer developed by GitHub (in collaboration with OpenAI and now powered by multiple models including Anthropic Claude and GitHub's own models).

It helps you write code faster by:

- Suggesting entire lines or blocks of code  
- Generating functions from comments/docstrings  
- Writing tests, documentation, commit messages, SQL, regex, etc.  
- Explaining existing code  
- Fixing bugs and refactoring  

---

# 2. Copilot Modes / Features

## Inline Suggestions
**Description:** Classic gray ghost text as you type  
**How to Use:** Just type – appears automatically  

## Copilot Chat (in IDE)
**Description:** Natural-language chat inside your editor  
**How to Use:** `Ctrl + Alt + I` (VS Code) or right-click → **Ask Copilot**

## Agent Mode
**Description:** Copilot can autonomously edit multiple files, run terminal commands  
**How to Use:** Available in VS Code Insiders + Copilot Nightly (2025) – toggle in Chat  

## Copilot Edits
**Description:** Multi-file, task-based editing (e.g., "migrate to React 19")  
**How to Use:** Chat → `/edit` or use **Copilot Edits** button  

## Copilot Workspace (Web)
**Description:** Plan & implement entire features in browser before coding  
**How to Use:** `workspace.github.com` (Enterprise only)

## Copilot in the CLI
**Description:** `gh copilot suggest` and `gh copilot explain` in terminal  
**How to Use:** Requires GitHub CLI extension  

## Copilot Completions
**Description:** Traditional single-file completions  
**How to Use:** Default behavior  

## Copilot Chat Slash Commands
**Description:** Quick actions inside chat  
**How to Use:** `/tests`, `/fix`, `/doc`, `/explain`, `/refactor`, etc.

---

# 3. GitHub Copilot Chat Interaction Modes

These modes determine how Copilot responds to your request and what actions it takes.

---

## 3.1 Ask Mode (Default Conversational Mode)

### What it does:
- Standard Q&A mode  
- Provides explanations, suggestions, or code snippets  
- No direct file changes  

### When to use:
- Learning and understanding code  
- Getting explanations  
- Exploring ideas before implementing  
- Asking "how to" questions  
- Code reviews and analysis  

---

## 3.2 Edit Mode (Inline Editing)

### What it does:
- Makes direct changes to your files  
- Modifies, adds, or deletes code  
- Shows a diff preview before applying  
- Allows accept/reject  

### When to use:
- Refactoring  
- Feature additions  
- Bug fixes  
- Multi-file updates  

### How to trigger:
- `/edit` command  
- VS Code → Select code → **Copilot > Edit**  
- Use modification-focused instructions  

---

## 3.3 Plan Mode (Multi-step Planning)

### What it does:
- Breaks complex tasks into steps  
- Creates execution strategy  
- Shows plan before execution  
- Executes after approval  

### When to use:
- Large refactoring  
- Multi-file changes  
- New features  
- Architectural updates  

### How to trigger:
- `/plan` command  
- Request complex multi-step tasks  

---

## 3.4 Agent Mode (Autonomous Task Execution)

### What it does:
- Highly autonomous  
- Can analyze, edit, run tests  
- Performs multi-step actions  

### When to use:
- Complex investigations  
- End-to-end feature implementation  
- Tasks requiring context gathering  

### How it works:
- Uses multiple agents (`@workspace`, `@terminal`, etc.)  
- Chains actions together  
- Makes independent decisions  

---
