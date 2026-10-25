---
name: first-principles
description: Reason from first principles about a claim, prediction, hypothetical, or engineering decision, and teach the user the moves while doing it. Use when the user says "first principles", asks whether a claim or forecast holds up, or asks to rebuild a design from scratch. "Coach me" switches to coaching mode.
---

# First principles

Reduce the question to claims that can be checked, rebuild the answer from only those, and name each **move** so the user can run it alone next time.

Two modes:

- **Analyze** (default): run the seven moves yourself, then end with the teaching block.
- **Coach** ("coach me", "my turn", "let me try"): the user runs each move and you grade it. See [Coach mode](#coach-mode).

## Claim grades

Every claim gets exactly one grade. This vocabulary is the core of the method, so use these words verbatim in the output.

| Grade | Kept? | Test |
|---|---|---|
| **measured** | kept | A number, a log line, a benchmark, a physical or legal limit you can point at |
| **mechanism** | kept | A cause-and-effect that holds whichever design is in use ("a wage falls toward the cost of the substitute", "a cache helps only if reads repeat") |
| **definition** | kept | True because of what the words mean |
| **objective** | kept | What the deciding actor is trying to achieve; it fixes direction even when size is open |
| **borrowed** | not yet | Believed because an expert, vendor, market, or habit says so |
| **resemblance** | not yet | Believed because this case looks like another case |
| **open** | blocks | Load-bearing, and no kept grade is available yet |

A borrowed or resemblance claim re-enters only after you dig it down to a kept grade.

Never fill an open claim with a plausible number. Write what would settle it instead: the command to run, the file to read, the metric to pull, or the source to check. In a repo, run the cheap check yourself before you grade the claim.

## The seven moves

Each move has a trigger question. The trigger questions are what the user should memorise.

1. **Pin the outcome.** *"What must be true at the end, for whom, by when?"*
   Done when a stranger could tell whether it was met. Give every fuzzy word a meaning: "high", "likely", "fast", "scalable". For a prediction, restate it in testable terms.

2. **Split the claim.** *"How many separate things have to be true here?"*
   List atomic claims, each of which could be false by itself. Mark the **conjunction**: the original holds only if every load-bearing claim holds. Done when every word that does work in the original belongs to a claim.

3. **Grade each claim.** *"Why do I believe this one?"*
   Give each claim a grade from the table above. For any claim about who gets what, or what people will do, **name the decider** and their objective. Done when every claim is graded, and each borrowed or resemblance claim says whose authority or which look-alike case carries it.

4. **Dig to bedrock.** *"What is this made of?"*
   Decompose each load-bearing claim that is not yet kept until it reaches a kept grade or stays open. Stop when a further split would not change the answer. When a quantity matters, check the order of magnitude (rate × time, units × uptime, share of the whole). Done when each load-bearing claim is kept or open.

5. **Delete and rebuild.** *"If today's solution had never been invented, what would I build?"*
   From the outcome and the kept claims only, write the simplest design or answer that works. Add a borrowed claim back only when the rebuild fails without it, and say which failure forced it back in. Done when every part of the rebuild traces to a kept claim or a logged failure.

6. **Read the gap.** *"Why is reality different from my rebuild?"*
   Put the rebuild beside the current design or the original claim. Each difference is a **convention** to drop, a **missed constraint** (add it to the kept claims and rebuild once more), or a **tradeoff** (name what is given up and what is gained). The convention often wins here: the existing tool returns as a missed constraint (maintenance, security, hiring, time). Say so plainly. Done when every difference has one of the three labels.

7. **Set the flip condition.** *"What would change my mind?"*
   Give the verdict, the weakest link, and the single observation that would reverse it. For engineering, also give the cheapest test that produces that observation. Done when the flip condition is something someone could actually observe.

Scale the depth to the stakes. For a small engineering call, run moves 1, 2, 3, and 7 in a few lines, and do moves 5 and 6 only when the grading finds a borrowed claim that carries the decision.

## Output

Lead with the verdict. Keep each section short. Use tables only for the claim grades.

```markdown
**Verdict:** [holds / likely because X / unlikely because Y / blocked on open claim Z]

**Outcome:** [one testable sentence]

**Claims**
| # | Claim | Grade | Why |
|---|---|---|---|

**Bedrock:** [what the load-bearing claims reduced to; open claims with the check that settles each]

**Rebuild:** [simplest answer from kept claims; any borrowed claim added back, and the failure that forced it]

**Gap:** [each difference labelled convention / missed constraint / tradeoff]

**Flip condition:** [the observation that reverses the verdict] · **Cheapest test:** [if engineering]

---
**The move that mattered:** [name one or two moves] — [one sentence on what that move exposed here that intuition missed]
**Ask yourself next time:** "[the trigger question, phrased for the user's own domain]"
**Your rep (2 min):** [one small exercise applying that move to something in the user's own work or life, not this example]
```

The teaching block is required. Pick the move that changed the answer, not the first move. Over successive runs, rotate the rep across moves so the user practises all seven.

## Coach mode

Run one move per turn.

1. State the move name and its trigger question. Ask the user to answer it for their topic.
2. Grade their answer against that move's "done when" criterion: **met**, **partly met**, or **missed**. Quote the part that works.
3. Show what you would add, in no more than three lines, and name the grade or move they skipped.
4. Move on to the next move. When the user has run moves 1–3 well twice in a row, offer to drop to coaching only on moves 4–7.

Finish with the full output template, filled in from their answers, so they can compare their run with yours.

## Worked examples

For three complete runs, read [examples.md](examples.md) when the topic type is unfamiliar: an engineering performance call, a convention-wins case, and a large forecast.
