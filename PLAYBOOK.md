# Day-to-day playbook (≈60 minutes to internalise)

**Stable layer (survives model churn):** rules + skills + specs + verifiers.  
**Volatile layer (ignore the hype):** which model, which harness button, which DAG runner binary.

Skills you already have (grill, prototype, tdd, code-review) stay. This playbook says **when to call which**, and where `/loop` and graphs fit without replacing them.

---

## The one workflow worth standardising

```
1. Grill     → decisions settled (human)
2. Spec      → written acceptance (artifact)
3. Prototype → optional; only if a design question is still open
4. TDD       → implement at agreed seams
5. Review    → Standards ‖ Spec until clean   ← your existing habit
6. (Rare) Graph → only when work fans out
7. (Rare) /loop → only when waiting on time/external signal
```

That sequence is **stable**. Models change how well each step runs; they do not change the sequence.

---

## Worked example: extend a settled seam

Pretend product asks: *"Add a `fragile` flag that adds 250p after remote surcharge, before express."*

### Minute 0–5 — Orient (no skill yet)

Open a **fresh chat** for this feature (new unit of work = new chat).

Say:

> Read `specs/<area>.md` and the project glossary if you have one. We are adding a `fragile` surcharge. Do not code yet.

### Minute 5–15 — `/grill`

> /grill Should fragile surcharge apply before or after express? What happens if weight is fractional?

Answer the frontier. Stop when the skill says the tree is empty. **Do not implement during grill.**

Write the decision into the spec (or ask the agent to).

### Minute 15–20 — Prototype? Usually skip

Only if you are unsure the *state model* or interaction feels right. For pure arithmetic or a settled API: **skip**. Prototype is for "does this feel right?", not for obvious logic.

### Minute 20–40 — `/tdd` (or `/poteto-mode` with a TDD done check)

> /tdd Implement fragile per specs/<area>.md. Seam: \<function\>. One failing test first.

Agent should:

1. Confirm seam with you
2. Add one failing test (red)
3. Minimal code (green)
4. Repeat if more cases

Verifier: your project's test command (`npm test`, `dotnet test`, `cargo test`, …).

### Minute 40–50 — Review until clean (your habit, named)

> Review this branch vs specs/<area>.md. Standards ‖ Spec until clean. Fixed point: main.

This is **your** PR cycling:

1. Run Standards + Spec review (parallel sub-agents if you have them)
2. If findings: fix them
3. Re-run review
4. Stop when both axes are empty (or only accepted judgement calls remain)

This is a **grind loop**, not `/loop`. With pstack: `/interrogate` then babysit.

### Minute 50–55 — When would a graph appear?

If the same change also needed independent work that does **not** share files (e.g. changelog + a second package's client types), then fan those out as a DAG.

Graph = **parallel independent nodes**, not "do my whole feature as a graph."

### Minute 55–60 — When would `/loop` appear?

You opened the PR. CI takes 8 minutes. You walk away.

> /loop 3m Check `gh pr checks` for this branch. Stop when all green or any red; summarise.

That is the **scheduler**. Different job from review-until-clean.

---

## Cheat sheet: what to type tomorrow

| Situation | Type this |
|-----------|-----------|
| Ambiguous product ask | `/grill` then update spec |
| Unclear UX / state machine | `/prototype` |
| Implementing a settled seam | `/tdd` or `/poteto-mode` + done check |
| Branch ready / PR polish | review-until-clean / `/interrogate` |
| Multi-package independent work | fan out as a DAG |
| Waiting on CI / deploy / webhook | `/loop 5m …` |
| Agent confused mid-feature | New chat + `@` the plan/spec; do not `/loop` |

---

## What to stop doing

1. **Putting your whole day in one chat** — context rot resets your "workflow" harder than model upgrades do.
2. **Calling every iteration a loop** — reserve `/loop` for wakes; keep skill names for procedures.
3. **Graphing linear work** — A→B→C as a DAG with no siblings is overhead with zero parallelism.
4. **Rebuilding skills every model release** — only change skills when a *failure mode* repeats, not when a blog posts a new pattern.
