# Day-to-day playbook: your pipeline × pstack

Generic operating guide for any product repo. Your discovery/planning stack stays. pstack owns rigor from **story ready → merged → proven on the real surface**.

---

## 1. What pstack actually is (5 pieces)

| Piece | What it is | You type |
|-------|------------|----------|
| **Router** | Matches a task to one of ~23 playbooks, copies steps into todos, calls other skills | `/poteto-mode …` |
| **Principles** | Named steering rules the mode loads; say the name mid-task to redirect | e.g. "laziness protocol", "prove it works" |
| **Parallel tools** | Same brief N ways (`/arena`), different slices (`/swarm`), multi-model review (`/interrogate`) | Directly, or mode calls them |
| **Verify skill** | Project-local "drive the real app + capture evidence" | `/create-verification-skill` once, then `/verify-<app>` |
| **Babysit + `/loop`** | Drive PR to merge-ready on a wake schedule | `/poteto-mode babysit this PR` (uses `/loop` internally) |

Everything else is situational. Default: **`/poteto-mode` + a done check**. Do not skill-shop every message.

**Setup once per machine:** `/setup-pstack` → pick models/budget → new chat.  
**Setup once per product repo:** `/create-verification-skill` (see §4 — this is your biggest unlock).

Keep `/poteto-mode` sticky for a session: pick it from `/` then **Alt+Enter** (Windows) / **Option+Enter** (Mac).

---

## 2. Your pipeline → what runs where

Adapt stage names to whatever you already use (PRD, epic, story, ticket). The shape matters more than the labels:

```
Product brief / PRD
  → Grill (ambiguity)          ← YOUR grilling skill (keep)
  → Design / architecture      ← /architect + /arena + prototype playbook
  → Epic / multi-PR plan       ← human/process artifact (keep)
  → Story / ticket specs       ← keep; become the "done check" in prompts
  → Implementation             ← /poteto-mode (feature | bug fix | refactoring)
  → Review → merge             ← /interrogate → babysit (+ /loop) → shipping
  → Prove on the real surface  ← /verify-<app> (+ whatever only humans can still do)
```

| Stage | Keep doing | Add from pstack | Skip / don't force |
|-------|------------|-----------------|--------------------|
| Brief / PRD | Human or PO writes it | — | Don't ask poteto to invent the product brief |
| Grill | Your grill skill | `/in-your-own-words` on noisy threads | `/poteto-mode` before decisions exist |
| Design | Architecture ideation | `/architect with checkpoint`, `/arena`, prototype playbook | Pure prose plans with no prototype when the shape is unclear |
| Multi-PR epic | Your planning artifact | Multi-phase plan playbook when epic → many PRs | Graphing a single story |
| Story specs | Write acceptance | Spec path named in every implement prompt | — |
| Implement | — | `/poteto-mode` feature/bug + `/tdd` when cheap | Micromanaging skill lists |
| Review→merge | Your review habit | `/interrogate` then babysit | Calling every re-review `/loop` |
| Real-surface prove | Manual smoke | **Verification skill** that agents can run | Trusting "it compiles" / unit tests alone |

pstack's author explicitly skips planning skills. **Your brief → grill → epic → story specs is the planning layer.** That is a feature, not a gap.

---

## 3. Prompts that match each stage

### A. After brief — grill (unchanged)

```text
/grill Here's the brief for <feature>. Frontier decisions only. Don't design or code.
```

### B. Design / architecture (when boundaries or data shapes are unclear)

```text
/architect with checkpoint. Design <X> for our <stack / system boundary>.
I care most about: what crosses the boundary, what stays pure, what stays in <layer>.
Stop and show me before implementing.
```

Competing options:

```text
/arena 3 candidates for <API or interaction>. Rubric: surface area, testability without the full stack, blast radius on existing layers.
```

Throwaway sketch:

```text
/poteto-mode prototype two options for <UX or flow>. Throwaway. Screenshots or a short drive of each. Don't touch production paths.
```

### C. Epic → stacked work (only when truly multi-PR)

```text
/poteto-mode turn the settled design + epic into a multi-phase plan.
Small verifiable PRs. Each PR names unit + live proof. Live proof must use /verify-<app> once it exists.
Don't implement.
```

### D. Story ready → implement

One story, one chat (or sticky poteto-mode):

```text
/poteto-mode new task. Implement story <id> per <path-to-story-spec>.
Done when: (1) acceptance bullets in the spec pass, (2) project tests you touch are green,
(3) /verify-<app> drives <named journey> and saves evidence.
Don't expand scope past this story.
```

Bug:

```text
/poteto-mode repro <symptom> first with /verify-<app> if possible. Root cause, then fix.
Done when repro is gone on the real surface. Show evidence.
```

### E. Review → merge (your old "review loop")

```text
/interrogate this branch vs <story-spec>. Skeptically. Act on / Consider only. No drive-by nits.
```

Then:

```text
/poteto-mode babysit PR <n>. Drive to merge-ready. Address real review threads, classify CI before retrying.
```

Babysit uses **`/loop`** under the hood to wake on CI/thread changes. That is the correct use of `/loop`. Your in-session fix cycles stay babysit/interrogate, not a hand-rolled timer.

Land only when you explicitly want merge:

```text
/poteto-mode ship / land the stack when ready.
```

### F. Real-surface proof

Until verify exists: you remain the verifier (manual smoke).  
After verify exists: agents must attach evidence; you still do a thin human pass on edge cases the harness cannot reach (licenses, hardware, proprietary data, staging-only flows).

```text
/poteto-mode run /verify-<app> against features/<journey>. Fail closed if doctor fails.
Then list what still needs a human pass.
```

---

## 4. Verification skill (do this once per product repo)

Agents cannot "feel" your real app the way they invent green unit tests. Without a verify skill, every `/poteto-mode` run ends with you as the bottleneck.

**In the product repo (not a lab), once:**

```text
/create-verification-skill
```

Tell it the truth when asked:

- Surface: what the user actually uses (web app, desktop host, CLI, API, …).
- What agents *can* drive today: browser/CDP, API calls, containerised smoke, focused integration tests.
- What only humans drive: licensed systems, hardware, installer, proprietary datasets, staging gates.
- Evidence: screenshots, assertions, log lines, test output.

Split the feature map honestly:

| Lane | Who proves it |
|------|----------------|
| Pure UI / happy path the harness can reach | Agent via verify skill |
| Contracts / APIs / pure domain | Agent via tests |
| Full environment / host / hardware | Human (or a future harness) |
| System tests in the real env | Human + growing automated suite |

`/maintain-verification-skill` on a cadence so the map doesn't rot.

Optional later: `cursor-team-kit` (`control-ui` / `control-cli`) if you want the same control surface pstack's multi-phase plans assume.

---

## 5. Weekly operating rhythm

| Cadence | Action |
|---------|--------|
| Each story | Sticky `/poteto-mode` + story spec path + done check naming `/verify-<app>` |
| Each PR | `/interrogate` once → babysit to merge-ready |
| Ambiguous product | Your grill — **not** poteto feature playbook |
| Hard boundary design | `/architect with checkpoint` before code |
| Same agent mistake 3× | `/correct` |
| Long painful run | `/reflect` → skill/rule edit |
| After verify exists | `/maintain-verification-skill` periodically |

---

## 6. What to ignore for 30 days

Do not start with: autopilot-full, overnight shipping, orchestrate, make-bot-ui, hillclimb, benny.

Earn those after: sticky poteto-mode on stories, one verify skill that actually launches something, babysit on real PRs.

---

## 7. One-page cheat sheet

```
Ambiguous product?     → /grill (yours)
Noisy bug report?      → /poteto-mode restate in your own words. no code yet.
Design boundary?       → /architect with checkpoint
Compare options?       → /arena or prototype playbook
Story ready?           → /poteto-mode + spec path + done check + /verify-<app>
Bug?                   → /poteto-mode repro first, then fix and verify
Branch review?         → /interrogate
PR to merge-ready?     → /poteto-mode babysit  (+ /loop wakes)
Waiting on CI only?    → /loop 5m check checks   (scheduler — not review)
Lost which skill?      → /poteto-help
```

---

## 8. First hour on a real product (ordered)

1. In the **product** repo: `/setup-pstack` (if not done on this machine).
2. `/create-verification-skill` — accept a partial harness over waiting for perfect env automation.
3. Pick one **already-specified** story. Sticky poteto-mode. Paste the §3D prompt with real paths.
4. On the PR: `/interrogate`, then babysit.
5. Note two checks only you can do; put them in the story's "human gate" list so agents stop pretending they're done.
