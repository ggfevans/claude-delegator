# Multi-Model Orchestration

You have access to external AI models via MCP tools. Use them autonomously based on these guidelines.

## Available Tools

| Tool | Provider | Use For |
|------|----------|---------|
| `mcp__codex__codex` | GPT | Architecture, debugging, code review |
| `mcp__codex__codex-reply` | GPT | Continue GPT conversation |
| `mcp__gemini__gemini` | Gemini | Research, docs, frontend, multimodal |
| `mcp__gemini__gemini-reply` | Gemini | Continue Gemini conversation |

## Phase 0: Delegation Check (EVERY message)

Before classifying a request, check if delegation would help:

| Signal | Action |
|--------|--------|
| External library/framework question | Delegate to Gemini (librarian role) |
| Complex architecture decision | Delegate to GPT (oracle role) |
| UI/UX implementation request | Delegate to Gemini (frontend-engineer role) |
| 2+ failed fix attempts on same issue | Escalate to GPT for fresh perspective |
| User explicitly asks for external opinion | Honor the request immediately |
| Research-heavy task (docs, best practices) | Delegate to Gemini |
| Security/performance analysis | Delegate to GPT |

## Response Handling (MANDATORY)

When external model returns a response:

1. **ALWAYS synthesize** - Never show raw output directly
2. **Extract insights** - Pull out key recommendations, concerns, code snippets
3. **Apply judgment** - External models can be wrong; evaluate their suggestions
4. **Disagree when warranted** - If you spot issues, say so and explain why
5. **Integrate with context** - Connect their response to the user's specific situation

### Example Synthesis

```
External model said: [summary of their response]

Key points:
- [insight 1]
- [insight 2]

My assessment: [your evaluation, any disagreements, how this applies to the task]
```

## Delegation Prompt Structure

**Core rule**: Include enough context for the question to be answerable without follow-ups.

Simple questions get simple prompts. Complex questions need more structure.

For complex tasks, consider including:

```
TASK: [What you need - one sentence]

CONTEXT:
- [Relevant background, code snippets, what's been tried]

CONSTRAINTS: [if any]

EXPECTED OUTPUT: [format you want back]
```

See `delegation-format.md` for examples and guidelines.

## Parallel Consultation

For critical decisions, you MAY consult both models in parallel:

```typescript
// Call both simultaneously for important architectural decisions
mcp__codex__codex({ prompt: "Analyze architecture tradeoffs..." })
mcp__gemini__gemini({ prompt: "Research implementation patterns..." })
```

Then synthesize their recommendations into a unified approach:
- GPT for architectural reasoning and tradeoffs
- Gemini for implementation patterns and best practices
- Your synthesis: combine both perspectives with your own judgment

## Escalation Pattern

After 2+ consecutive failures on the same issue:

1. **Document what you've tried** - List approaches and why they failed
2. **Suggest escalation** - "I've tried X and Y without success. Should I escalate to GPT for a fresh perspective?"
3. **Wait for user approval** - Don't escalate automatically
4. **Provide full context** - When escalating, include failure history

## Cost Awareness

External model calls cost money. Use them strategically:

- **Don't spam** - One well-structured delegation beats five vague ones
- **GPT is expensive** - Reserve for architecture, security, complex debugging
- **Gemini is cheaper** - Use freely for research, docs, frontend work
- **Avoid redundant calls** - If you already have the answer, don't delegate

## Anti-Patterns

| Don't Do This | Do This Instead |
|---------------|-----------------|
| Delegate trivial questions | Answer directly |
| Show raw external output | Synthesize and interpret |
| Delegate without context | Include enough context to answer |
| Ignore external model errors | Investigate and retry or escalate |
| Spam multiple calls | One structured call |
| Auto-escalate without asking | Suggest escalation, wait for approval |
