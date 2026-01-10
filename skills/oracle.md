---
description: Consult GPT as a strategic advisor for architecture decisions, debugging escalation, code review, or security analysis. Use when facing complex technical decisions, after 2+ failed fix attempts, or when you need a fresh perspective on difficult problems.
---

# Oracle Consultation

Invoke the Oracle (GPT via Codex) for strategic technical consultation.

## When to Use

- Complex architecture decisions with long-term impact
- After 2+ consecutive failed fix attempts
- Security or performance concerns requiring deep analysis
- Code review for significant changes
- Multi-system tradeoffs needing external perspective

## How to Invoke

Use the `mcp__codex__codex` tool with the oracle role prompt. Structure your request using the 7-section delegation format:

```
Read the oracle role prompt from: ${CLAUDE_PLUGIN_ROOT}/prompts/oracle.md

Then invoke:
mcp__codex__codex({
  prompt: "[Include oracle prompt] + [Your structured request]",
  developer-instructions: "You are the Oracle. Follow the oracle.md system prompt."
})
```

## Delegation Format (Required)

Your prompt to the Oracle MUST include:

1. **TASK**: One sentence - atomic, specific goal
2. **EXPECTED OUTCOME**: What success looks like
3. **CONTEXT**: Current approach, relevant code, background
4. **CONSTRAINTS**: Technical limitations, patterns to follow
5. **MUST DO**: Exhaustive list of requirements
6. **MUST NOT DO**: Forbidden actions
7. **OUTPUT FORMAT**: How to structure the response

## Response Handling

When Oracle responds:
1. **Synthesize** - Never show raw output directly
2. **Extract key insights** - Pull out recommendations and concerns
3. **Apply judgment** - Oracle can be wrong; evaluate suggestions critically
4. **Integrate** - Connect their response to the user's specific situation

## Anti-Patterns

- Don't use Oracle for simple file operations
- Don't use Oracle on first fix attempt
- Don't use Oracle for trivial decisions
- Don't spam multiple vague calls; one structured call is better
