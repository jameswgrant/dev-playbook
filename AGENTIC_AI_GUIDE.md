# Professional development with agentic AI

**A step-by-step guide, as of October 2026**

> **The idea in one line:** the model is replaceable; your *process* is not.
> (Find the path) → grill → spec → (prototype) → test-first → review until clean → retro. Models change how well each step runs. They do not change the steps.

---

## Contents

1. [How an agent actually works](#1-how-an-agent-actually-works)
2. [Models: which one, when](#2-models-which-one-when)
3. [MCP: giving the agent tools](#3-mcp-giving-the-agent-tools)
4. [Skills: giving the agent procedures](#4-skills-giving-the-agent-procedures)
5. [One-time setup (≈10 minutes)](#5-one-time-setup-10-minutes)
6. [The workflow, step by step](#6-the-workflow-step-by-step)
7. [Worked example: a 60-minute feature](#7-worked-example-a-60-minute-feature)
8. [Cheat sheet: what to type](#8-cheat-sheet-what-to-type)
9. [Rules that keep you safe](#9-rules-that-keep-you-safe)
10. [What to stop doing](#10-what-to-stop-doing)

---

## 1. How an agent actually works

An agent is a **loop**:

```
you ask  →  model reads its context window  →  model asks for a tool
         →  the app runs the tool  →  result goes back into the window  →  repeat
```

Three things to hold onto:

| Fact | What it means for you |
|---|---|
| The model only sees its **context window** | If a file, decision, or rule isn't in the window, the model doesn't know it. No hidden memory of your repo. |
| The **app** runs the tools, not the model | Cursor, Claude Code, Codex, Copilot read files, run commands, and call services on the model's behalf. |
| "Done" is a **claim**, not a fact | Tests, types, and review decide whether it's done. |

**Your job has moved:** less typing code, more choosing what goes into the window, which tools are allowed, and how the result gets verified.

---

## 2. Models: which one, when

There is no single best model. Pick by **task difficulty** and **cost**.

| Job | Good choices right now |
|---|---|
| Hard design, gnarly bugs, big refactors | Claude Opus 5.5, Claude Fable 5.1, GPT-6 Astra, Gemini 4 Argon |
| Everyday coding | Claude Sonnet 5.5, GPT-6.1 Sol, Grok 4.7, Gemini 3.8 Flash |
| Cheap, fast, mechanical | Claude Haiku 5.5, Flash-Lite-class models |
| Open-weights | Kimi K3, DeepSeek V4, GLM-5, Qwen |

What actually differs between models:

- **Finish rate** on multi-step work (does it stop halfway?)
- **Price** per million tokens. Output costs more than input.
- **Context size**, about 500K–1M tokens. Bigger isn't smarter: reasoning gets sloppier as the window fills, so treat roughly **150K** as the sharp zone.
- **Effort / thinking level**. Higher spends more tokens on a better attempt.

> 💡 Benchmarks describe a model *plus somebody else's setup*. Your own ten real tasks are a better benchmark than any leaderboard.

---

## 3. MCP: giving the agent tools

**MCP (Model Context Protocol)** is the open standard for plugging tools and data into any agent. Write a server once; it works in Cursor, Claude, ChatGPT, Copilot, and others.

- **Spec:** `2026-07-28` (stateless request/response, OAuth-based auth)
- **Home:** the Agentic AI Foundation (Linux Foundation): [modelcontextprotocol.io](https://modelcontextprotocol.io)
- **Ecosystem:** ~50,000 public servers, mostly hosted remote endpoints

An MCP server exposes three things:

| Piece | What it is | Example |
|---|---|---|
| **Tools** | Functions the model can call | `create_issue`, `run_query` |
| **Resources** | Data the model can read | A ticket, a doc, a table |
| **Prompts** | Templates the server offers | "Summarise this incident" |

**Typical servers:** GitHub, Linear/Jira, Notion, your database, your own internal API.

> ⚠️ **Treat every MCP server like credentials you handed a new hire.** The model will follow instructions it reads inside tool output (*prompt injection*). Start read-only. Give write access only where you'd give it to someone on day one. Use tokens you can revoke.

---

## 4. Skills: giving the agent procedures

A **skill** is a folder with a `SKILL.md` file. It teaches the agent *how you do a task*. It's an open standard ([agentskills.io](https://agentskills.io)) supported by Cursor, Claude, ChatGPT, Copilot, VS Code, and 70+ others.

```text
my-skill/
├── SKILL.md       # required: name, description, instructions
├── scripts/       # optional: code the agent can run
├── references/    # optional: docs, loaded only when needed
└── assets/        # optional: templates
```

The agent only sees each skill's **name and description** until a task matches. Then it loads the full instructions. You can also call one directly by typing `/skill-name`.

### Where things go

| Put it in… | When it's… | Example |
|---|---|---|
| **`AGENTS.md`** (or a Cursor rule) | Always true for this repo | Build command, style rules, "never touch `/legacy`" |
| **A skill** | A procedure you repeat | "How we do TDD", "how we review a PR" |
| **An MCP server** | A side effect or live data | Open a ticket, query prod read-only |

---

## 5. One-time setup (≈10 minutes)

This guide uses **Matt Pocock's skills**, a well-tested set built for real engineering work.
Repo: **[github.com/mattpocock/skills](https://github.com/mattpocock/skills)**

### Step 1: Check whether they're installed (1 minute)

In Cursor's agent chat, type `/grill`. If you see `grill-me` and `grill-with-docs` in the menu, they're installed: **skip to Step 3.**

### Step 2: Install them (3 minutes)

**Cursor** (or most other agents):

```bash
npx skills@latest add mattpocock/skills -a cursor
```

When it asks which skills to take, **include `setup-matt-pocock-skills`**.

**Claude Code:**

```bash
claude plugin install mattpocock-skills@claude-plugins-official
```

To update later: `npx skills@latest update`

### Step 3: Configure each repo (5 minutes, once per repo)

Open the repo in your agent and run:

```text
/setup-matt-pocock-skills
```

It asks three things: where your issues live (GitHub, GitLab, local files, Jira/Linear), which triage labels you use, and where design docs go. It writes small config files under `docs/agents/` that the other skills read.

### Step 4: Add an `AGENTS.md` (2 minutes)

At the repo root, write the things that are *always* true:

```markdown
# AGENTS.md
- Test: `npm test`   Typecheck: `npm run typecheck`
- Follow existing patterns in the folder you're editing.
- Never commit secrets. Never edit generated files in /dist.
```

✅ **Setup done.** You now have the skills, a per-repo config, and always-on rules.

---

## 6. The workflow, step by step

```text
0. Find the path → only if the work is too big or foggy for one session
1. Grill         → decisions settled             (you decide)
2. Spec          → written acceptance criteria   (an artifact)
3. Prototype     → only if a design question is still open
4. TDD           → implement at agreed seams, one test at a time
5. Review        → Standards + Spec, until clean
6. Retro         → improve the setup so the next session goes better
```

> 🧠 **One feature = one fresh chat.** Starting clean costs nothing. A long, muddled chat costs a lot.

### Step 0: Is the path clear? If not, use Wayfinder

Ask yourself one question: **could I settle every decision in one sitting?**

| Answer | Do this |
|---|---|
| **Yes**: a normal feature or bug | Skip to **Step 1**. |
| **No**: a greenfield project, a big migration, or "we know where we want to end up, not how to get there" | Run `/wayfinder` first. |

```text
/wayfinder <the loose idea, e.g. "move billing from Stripe Checkout to our own flow">
```

Wayfinder charts the route instead of charging at the destination:

1. **Names the destination.** It grills you on what "done" means: a spec, a locked decision, or a migration.
2. **Draws a map.** One issue on your tracker, labelled `wayfinder:map`. It holds the destination, the decisions so far, and the **fog**, meaning questions you can see coming but can't phrase yet.
3. **Creates decision tickets.** Each child issue is one question, sized to a single session. Each has a type:
   - **Grilling:** a conversation with you (the default)
   - **Research:** the agent reads docs or APIs on its own
   - **Prototype:** a rough artifact to react to
   - **Task:** setup that unblocks a decision, like getting API access
4. **Wires blocking links,** so your tracker shows which tickets you can take right now.

Then work it, **one ticket per fresh chat**:

```text
/wayfinder <map link>
```

It picks the next unblocked ticket, resolves it with you, records the answer, closes it, and turns any fog that has become clear into new tickets.

**When the map is clear, Wayfinder hands off; it doesn't build.** Join the main flow at **Step 2** with `/to-spec`. It turns the map's decisions into a buildable plan. Then run `/to-tickets` and `/implement`.

> ⚠️ Wayfinder is slow and demanding by design. Don't use it on a well-scoped feature. If it finds no fog, it will tell you to skip it. Needs `/setup-matt-pocock-skills` done first, because the map lives on your issue tracker.

### Step 1: Grill: settle decisions before code

```text
/grill-with-docs <describe the feature or paste the ticket>
```

- The agent interviews you in **rounds**: numbered questions, each with its **recommended answer**.
- It looks up **facts** itself (code, docs). **Decisions** are yours.
- It stops when nothing is left silently assumed.
- `grill-with-docs` records the decisions in `CONTEXT.md` and ADRs. Outside a repo, use `/grill-me`.

**Rule:** no code during grilling.

### Step 2: Write it into a spec

```text
/to-spec
```

Or just ask: *"Write the decisions into `specs/<area>.md` as acceptance criteria."*
The spec is what the code will be **judged against** in Step 5.

> For multi-day work, follow with `/to-tickets` to split it into small, independent tickets. Then build each one in its own fresh chat with `/implement`.

### Step 3: Prototype (only if needed)

```text
/prototype Does this state model feel right for <thing>?
```

Use it when you can't tell **on paper** whether a state machine or a UI *feels* right. It produces throwaway code that answers **one question**. Fold the answer back into the spec.

**Skip it** for settled arithmetic, known APIs, or obvious logic.

### Step 4: Build it test-first

```text
/tdd Implement <feature> per specs/<area>.md. Seam: <function or module>.
```

The agent should:

1. **Confirm the seam** with you. That's the public boundary to test at.
2. Write **one failing test** (red).
3. Write the **minimal code** to pass it (green).
4. Repeat for the next case.

Tests describe **behaviour**, not internals, so they survive refactors.
For a whole spec or ticket, use `/implement`. It runs TDD internally and finishes with a review.

### Step 5: Review until clean

```text
/code-review Fixed point: main. Spec: specs/<area>.md
```

Two reviews run in parallel:

| Axis | Question |
|---|---|
| **Standards** | Does the code follow *this repo's* rules? |
| **Spec** | Does the code do *what the spec asked*? |

Then grind:

1. Fix the findings.
2. Re-run `/code-review`.
3. **Stop** when both axes are empty, or only judgement calls you've accepted remain.

✅ **Open the PR.**

### Step 6: Retro: make the next session better

Before closing the chat, run:

```text
/retro
```

The code is done. A retro improves the **environment the agent works in**, so the same friction doesn't come back. It reads the session and suggests fixes, worst first:

| It looks for… | Typical fix |
|---|---|
| The agent took ages finding a file | A one-line pointer in `AGENTS.md` |
| A mistake a machine could have caught | A lint rule, type, or test |
| A mistake the review missed | A new rule for `/code-review` to enforce |
| The agent lacked key information | Dev-server logs, read-only access to a service |
| Expensive or wasteful tool calls | A leaner script or MCP tool |
| Bloated instructions that change nothing | Delete them |

You choose which suggestions to apply. Keep `AGENTS.md` short. Coding rules belong in the standards the reviewer checks, because the reviewer has more room in its context window than the agent doing the work.

> 💡 This is how the setup gets better over time: **change things when a real session failed, not when a blog post says so.**

---

## 7. Worked example: a 60-minute feature

> **Ask from product:** *"Add a `fragile` flag that adds 250p after the remote surcharge, before express."*

| Time | Do this | Type |
|---|---|---|
| 0–5 min | **Fresh chat.** Orient, no code. | *"Read `specs/shipping-quote.md` and `CONTEXT.md`. We're adding a `fragile` surcharge. Don't code yet."* |
| 5–15 min | **Grill.** Answer each round. | `/grill-with-docs Should fragile apply before or after express? What if weight is fractional?` |
| 15–20 min | **Spec.** Record the decision. | *"Add to the spec: `fragile: true` adds 250p after remote surcharge, before the express multiplier."* |
| 20 min | **Prototype?** No. It's settled arithmetic. | *(skip)* |
| 20–40 min | **TDD** at the agreed seam. | `/tdd Implement fragile per specs/shipping-quote.md. Seam: quoteShipping(). One failing test first.` |
| 40–50 min | **Review until clean.** | `/code-review Fixed point: main.` → fix → re-run |
| 50–55 min | **PR + wait for CI** without babysitting. | `/loop 3m Check gh pr checks for this branch. Stop when all green or any red; summarise.` |
| 55–60 min | **Retro.** Apply the top suggestion. | `/retro` |

---

## 8. Cheat sheet: what to type

| Situation | Type this |
|---|---|
| Huge or foggy effort, path unclear | `/wayfinder` → one ticket per chat → `/to-spec` |
| Ambiguous product ask | `/grill-with-docs` → then update the spec |
| Unclear UX or state machine | `/prototype` |
| Spec is settled, ready to build | `/tdd` (one behaviour) or `/implement` (a whole ticket) |
| Branch ready / PR polish | `/code-review` → fix → repeat until clean |
| Big feature, multiple sessions | `/to-spec` → `/to-tickets` → `/implement` per ticket, fresh chat each |
| Something's broken and stubborn | `/diagnosing-bugs` |
| Independent work that touches different files | Split into parallel subagents, one per piece |
| Waiting on CI / deploy / webhook | `/loop 5m <what to check and when to stop>` |
| Agent confused mid-feature | **New chat** + `@` the spec. Don't push on. |
| Session finished, or agent kept stumbling | `/retro` → apply the top fix |
| Lost track of which skill to use | `/ask-matt` |

---

## 9. Rules that keep you safe

1. **Context is curated, not dumped.** Point at the 3–5 files that matter, not "read the whole repo".
2. **Verification is non-negotiable.** Types, tests, review. The agent's "done" is a claim.
3. **Secrets stay out of chat.** No production tokens in prompts. MCP gets revocable, least-privilege tokens.
4. **Read every diff before it merges.** You own the code, not the model.
5. **Keep a tiny eval set.** About 10 real tasks from your codebase. Re-run them when you change model, skill, or tool.

---

## 10. What to stop doing

1. **Putting your whole day in one chat.** Context rot hurts more than a weaker model.
2. **Calling every retry a "loop".** `/loop` is for *waiting on time or an external signal*. Review-until-clean is a grind you drive.
3. **Parallelising linear work.** A → B → C has nothing to split. Only fan out work that's genuinely independent.
4. **Rewriting your skills every model release.** Change a skill only when the same failure keeps repeating.
5. **Letting the agent code before decisions are settled.** Grill first. Always.

---

### ⏱️ Your first 2 minutes

Open your agent on a real repo, pick one small, slightly ambiguous ticket, and type:

```text
/grill-with-docs <paste the ticket>
```

Answer the first round of questions. That's the habit everything else builds on.
