# Glossary — what people actually mean

Skills and MCPs you already know. These three words are where people talk past each other.

## Skill

A **playbook the agent loads on demand** (`SKILL.md`). Procedural: "when X, do steps 1…n."

Grill / prototype / tdd / code-review skills are this. They are *not* timers and *not* parallel graphs — they are instructions for *how* one agent (or its sub-agents) should behave when that workflow is relevant.

| Mechanism | Always in context? | You invoke with |
|-----------|--------------------|-----------------|
| Rule (`.cursor/rules`) | Yes | Automatic |
| Skill | Only when relevant or `/name` | Agent picks it, or you slash-command it |
| MCP | Tools appear when connected | Agent calls tools |

## Loop — two different things, same English word

### A) Cursor `/loop` (scheduler wake)

**What it is:** wake this chat again on a clock (or on a watched event), run a prompt, sleep, repeat.

**Examples that fit `/loop`:**

- `/loop 5m check if CI is green on this PR`
- `/loop 10m pull latest deploy logs and summarise regressions`
- "keep watching until the preview URL responds 200"

**Examples that do NOT fit `/loop`:**

- "review → fix comments → re-review until clean" ← that is a **skill procedure** (or a stop-hook grind), not a timer
- "red → green TDD" ← that is the **tdd skill's internal cycle**, not `/loop`

`/loop` answers: *"when should the agent wake up again?"*  
Your PR cycling answers: *"what should the agent do next until a verifier says stop?"*

### B) Skill / grind loop (iterate until done)

**What it is:** stay on the same objective; after each attempt, check a verifier; if not done, continue.

**Day-to-day PR review cycling is this.** Same shape as:

- TDD red→green until the slice is done
- Stop-hook "grind until tests pass"
- Review-until-clean / `/interrogate` → fix → re-review

Verifiers you already use without calling them that:

| Goal | Verifier (stop when…) |
|------|------------------------|
| Feature works | project tests green + typecheck |
| PR ready | no open review threads / CI green |
| Design settled | you said "ship it" after grill rounds |
| Real surface works | `/verify-<app>` evidence attached |

## Graph (DAG)

**What it is:** a **Directed Acyclic Graph** of subtasks — nodes with `depends_on`, no cycles. Independent nodes in the same "rank" run **in parallel** (separate sub-agents). Dependent nodes wait.

```
  research-rates ──┐
                   ├──► design-api ──► implement ──┬──► write-tests
  research-edge ───┘                               └──► update-docs
```

**Use a graph when:** parts of the work do not need each other and would race or bloat one context window if serialised.

**Do not use a graph when:** the work is one seam, one file, or each step needs the previous step's *judgment* (grill rounds, TDD slices). Those stay linear skills.

| Pattern | Parallel? | Dependency-aware? | Typical trigger |
|---------|-----------|-------------------|-----------------|
| One chat, one skill | No | Implicit in the playbook | `/tdd`, `/grill` |
| Sub-agents in a skill | Yes (skill author spawns them) | Soft | code-review's Standards ‖ Spec |
| DAG / graph runner | Yes, by rank | Explicit `depends_on` | "fan this out", migration, multi-package change |
| `/loop` | N/A (time) | N/A | wait for CI / deploy / log line |

## How PR review cycling maps

```
You today:  review → address → re-review → … → clean
            └──────── skill / grind loop ────────┘

Not the same as:
            /loop 5m "is the PR clean yet?"
            └─ scheduler that polls; thin prompt each wake
```

Polling with `/loop` *can* wrap a review skill ("every 10m run review-until-clean on PR #42") when the blocker is **external** (waiting on human review, waiting on CI). While *you* are driving fixes in-session, use the skill loop, not the timer.

## Side-by-side

| | Skill / grind loop | `/loop` (scheduler) |
|--|--------------------|---------------------|
| Stays in chat? | Yes | Wakes, runs, sleeps |
| Trigger | Verifier failed / findings remain | Clock or watched event |
| Typical invoke | `/tdd`, review-until-clean, babysit internals | `/loop 5m check checks` |
| Wrong use | — | `/loop 2m review and fix everything` |
