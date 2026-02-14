---
name: setup
description: Configure claude-delegator with Codex MCP server
allowed-tools: Bash, Read, Write, Edit, AskUserQuestion
timeout: 60000
---

# Setup

Configure Codex (GPT) as specialized expert subagents via native MCP. Five domain experts that can advise OR implement.

## Step 1: Check Codex CLI

```bash
which codex 2>/dev/null && codex --version 2>&1 | head -1 || echo "CODEX_MISSING"
```

### If Missing

Tell user:
```
Codex CLI not found.

Install with: npm install -g @openai/codex
Then authenticate: codex login

After installation, re-run /claude-delegator:setup
```

**STOP here if Codex is not installed.**

## Step 2: Configure MCP Server

Register Codex as an MCP server using Claude Code's native command:

```bash
claude mcp add --transport stdio --scope user codex -- codex -m gpt-5.3-codex mcp-server
```

This registers the Codex MCP server at user scope (available across all projects).

**Note:** To customise Codex behaviour, add CLI flags before `mcp-server`:
- `-p nosandbox` — disable sandbox restrictions
- `-c 'model_reasoning_effort="xhigh"'` — set reasoning effort
- Example with all options:
  ```bash
  claude mcp add --transport stdio --scope user codex -- codex -p nosandbox -m gpt-5.3-codex -c 'model_reasoning_effort="xhigh"' mcp-server
  ```

## Step 3: Install Orchestration Rules

```bash
mkdir -p ~/.claude/rules/delegator && cp ${CLAUDE_PLUGIN_ROOT}/rules/*.md ~/.claude/rules/delegator/
```

## Step 4: Verify Installation

Run these checks and report results:

```bash
# Check 1: Codex CLI version
codex --version 2>&1 | head -1

# Check 2: MCP server registered and model version
CODEX_CONFIG=$(claude mcp get codex 2>/dev/null)
if echo "$CODEX_CONFIG" | grep -q "codex"; then
  MODEL=$(echo "$CODEX_CONFIG" | grep -oE 'gpt-[0-9]+\.[0-9]+-?[a-z]*' | head -1)
  echo "OK (model: ${MODEL:-unknown})"
else
  echo "NOT CONFIGURED"
fi

# Check 3: Rules installed (count files)
ls ~/.claude/rules/delegator/*.md 2>/dev/null | wc -l

# Check 4: Auth status (check if logged in)
codex login status 2>&1 | head -1 || echo "Run 'codex login' to authenticate"
```

## Step 5: Report Status

Display actual values from the checks above:

```
claude-delegator Status
───────────────────────────────────────────────────
Codex CLI:     ✓ [version from check 1]
Model:         ✓ [model from check 2] (or ✗ if not configured)
MCP Config:    ✓ Registered via claude mcp (or ✗ if missing)
Rules:         ✓ [N] files in ~/.claude/rules/delegator/
Auth:          [status from check 4]
───────────────────────────────────────────────────
```

If any check fails, report the specific issue and how to fix it.

## Step 6: Final Instructions

```
Setup complete!

Next steps:
1. Restart Claude Code to load MCP server
2. Authenticate: Run `codex login` in terminal (if not already done)

Five GPT experts available:

┌──────────────────┬─────────────────────────────────────────────┐
│ Architect        │ "How should I structure this service?"      │
│                  │ "What are the tradeoffs of Redis vs X?"     │
│                  │ → System design, architecture decisions     │
├──────────────────┼─────────────────────────────────────────────┤
│ Plan Reviewer    │ "Review this migration plan"                │
│                  │ "Is this implementation plan complete?"     │
│                  │ → Plan validation before execution          │
├──────────────────┼─────────────────────────────────────────────┤
│ Scope Analyst    │ "Clarify the scope of this feature"         │
│                  │ "What am I missing in these requirements?"  │
│                  │ → Pre-planning, catches ambiguities         │
├──────────────────┼─────────────────────────────────────────────┤
│ Code Reviewer    │ "Review this PR"                            │
│                  │ "Find issues in this implementation"        │
│                  │ → Code quality, bugs, maintainability       │
├──────────────────┼─────────────────────────────────────────────┤
│ Security Analyst │ "Is this authentication flow secure?"       │
│                  │ "Harden this endpoint"                      │
│                  │ → Vulnerabilities, threat modeling          │
└──────────────────┴─────────────────────────────────────────────┘

Every expert can advise (read-only) OR implement (write).
Expert is auto-detected based on your request.
Explicit: "Ask GPT to review..." or "Have GPT fix..."
```

## Step 7: Ask About Starring

Use AskUserQuestion to ask the user if they'd like to ⭐ star the claude-delegator repository on GitHub to support the project.

Options: "Yes, star the repo" / "No thanks"

**If yes**: Check if `gh` CLI is available and run:
```bash
gh api -X PUT /user/starred/jarrodwatts/claude-delegator
```

If `gh` is not available or the command fails, provide the manual link:
```
https://github.com/jarrodwatts/claude-delegator
```

**If no**: Thank them and complete setup without starring.
