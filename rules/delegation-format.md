# Delegation Prompt Guidelines

How to structure prompts when delegating to external models.

## Core Principle

**Include enough context for the question to be answerable without follow-ups.**

That's it. Simple questions get simple prompts. Complex questions need more structure.

## When Simple is Fine

For straightforward questions, just ask:

```
"Is this JWT implementation vulnerable to timing attacks?"

"What's the recommended way to handle optimistic updates in React Query v5?"

"Review this function for edge cases: [code]"
```

No ceremony needed. The question contains enough context.

## When Structure Helps

Use structured prompts when:
- Multiple pieces of context are needed
- The task has specific constraints
- Prior attempts have failed and you need to communicate history
- The expected output format matters

## Structured Format (When Needed)

```
TASK: [What you need - one sentence]

CONTEXT:
- [Relevant background]
- [Code snippets or file references]
- [What's been tried, if applicable]

CONSTRAINTS: [if any]
- [Technical limitations]
- [Patterns to follow]

EXPECTED OUTPUT:
- [What format you want back]
```

That's 4 sections max. Use what you need, skip what you don't.

---

## Examples

### Simple Research Question

```
What's the best practice for handling file uploads larger than
100MB in Express.js? We're using multer currently.
```

### Architecture Review (More Context Needed)

```
TASK: Review this caching strategy for potential issues.

CONTEXT:
- We cache user sessions in Redis with 24h TTL
- ~50k active users, peak 5k concurrent
- Current code: [snippet]

CONSTRAINTS:
- Must maintain session consistency across multiple app servers
- Cannot increase Redis memory beyond 2GB

EXPECTED OUTPUT:
- Issues identified with severity
- Recommended fixes
```

### Debugging After Failed Attempts

```
TASK: Help debug why WebSocket connections drop after exactly 60 seconds.

CONTEXT:
- Using Socket.io 4.x with nginx reverse proxy
- Connections work fine locally, only fail in production
- Already tried: increasing nginx proxy_read_timeout, Socket.io pingInterval

WHAT I'VE OBSERVED:
- Chrome DevTools shows clean disconnect (code 1000)
- No errors in server logs
- Happens regardless of client activity

What am I missing?
```

---

## Role-Specific Tips

### Oracle (GPT) - Architecture/Review
- Include relevant code snippets, not just descriptions
- State the tradeoffs you're considering
- Ask for reasoning, not just answers

### Librarian (Gemini) - Research
- Specify versions that matter
- Say what you've already found insufficient
- Ask for working examples, not theory

### Frontend Engineer (Gemini) - UI Code
- Reference existing component patterns in your codebase
- Specify styling approach (Tailwind, CSS-in-JS, etc.)
- Mention accessibility requirements upfront

---

## Anti-Patterns

| Don't | Why |
|-------|-----|
| "Help me with this code" | Zero context - what help? |
| Dumping entire files | Wastes tokens, obscures problem |
| 7-section format for "how do I X?" | Overkill, slows you down |
| Omitting version numbers | Leads to outdated advice |
| Asking without showing code | Forces assumptions |

---

## The Test

Before delegating, ask: "Would a senior engineer be able to help with just this prompt?"

If yes, send it. If no, add the missing context.
