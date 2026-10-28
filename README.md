# Dev playbook

Stable day-to-day workflow for shipping with agents. Survives model churn; ignores harness hype.

## Start here

| Doc | When you need it |
|-----|------------------|
| [AGENTIC_AI_GUIDE.md](./AGENTIC_AI_GUIDE.md) | New to agentic AI: models, MCP, skills, and the step-by-step workflow with Matt Pocock's skills |
| [DAY_TO_DAY_PSTACK.md](./DAY_TO_DAY_PSTACK.md) | Your full pipeline × pstack (grill → implement → verify → merge) |
| [PLAYBOOK.md](./PLAYBOOK.md) | One feature end-to-end (~60 min to internalise) |
| [GLOSSARY.md](./GLOSSARY.md) | Skills vs `/loop` vs graphs (DAG) — the naming trap |

## Stable vs volatile

**Stable (keep investing here):** rules · skills · specs · verifiers  
**Volatile (ignore the blog cycle):** which model · which harness button · which DAG runner binary

## The sequence

```
1. Grill     → decisions settled (human)
2. Spec      → written acceptance (artifact)
3. Prototype → optional; only if a design question is still open
4. TDD       → implement at agreed seams
5. Review    → Standards ‖ Spec until clean
6. (Rare) Graph → only when work fans out
7. (Rare) /loop → only when waiting on time/external signal
```

Default implementation driver once a story is ready: **`/poteto-mode` + a done check**. Do not skill-shop every message.
