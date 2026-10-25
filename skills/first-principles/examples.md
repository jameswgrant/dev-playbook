# Worked examples

Three runs, each showing a different shape. Numbers marked *(given)* belong to the scenario. In a real run, each one is either measured or listed as open with its check.

## 1. Engineering: "The API is slow, add Redis"

**Verdict:** Blocked on one open claim. Measure before you add infrastructure.

**Outcome:** p95 of `GET /orders` under 300 ms for logged-in users, by next release.

| # | Claim | Grade | Why |
|---|---|---|---|
| 1 | The endpoint is slow | measured | p95 is 1.8 s *(given)* |
| 2 | The time is spent on the database | open | No trace yet |
| 3 | The same reads repeat across requests | open | Hit rate is unknown |
| 4 | A cache fixes slow endpoints | borrowed | The usual advice, not this system |
| 5 | A cache helps only if reads repeat and can be stale for a while | mechanism | Holds for any cache |

**Bedrock:** Claim 4 reduces to claim 5 plus claims 2 and 3. Settle claim 2 with a request trace or `EXPLAIN ANALYZE` on the top query. Settle claim 3 by counting distinct query keys per minute in the logs.

**Rebuild:** If claim 2 shows one N+1 query, the rebuild is a join and an index, and needs no new service.

**Gap:** Redis is a convention here until claims 2 and 3 are measured. If both come back true and the data can be 30 s stale, Redis returns as a tradeoff: an extra service to run in exchange for latency.

**Flip condition:** The trace shows over 70% of time in repeated identical reads. **Cheapest test:** one traced request in staging (10 min).

---
**The move that mattered:** Split the claim. "Add Redis" hid two open claims behind a borrowed one.
**Ask yourself next time:** "What would have to be true about my traffic for this fix to work?"
**Your rep (2 min):** Take the last fix you proposed and write the one claim about the system it silently assumed.

## 2. Convention wins: "We should write our own auth"

**Verdict:** Unlikely. The rebuild brings the standard library back as a missed constraint.

**Outcome:** Employees sign in to one internal app, sessions last a workday, and no account can be taken over.

| # | Claim | Grade | Why |
|---|---|---|---|
| 1 | We only need cookie sessions and a password check | measured | One app, no federation *(given)* |
| 2 | Auth is simple code | resemblance | Looks like a small CRUD feature |
| 3 | Credential storage, reset flows, and rate limits each fail silently when wrong | mechanism | A security bug produces no error until someone exploits it |
| 4 | The team owns whatever it writes indefinitely | mechanism | Code you write is code you maintain |

**Rebuild:** From claims 1, 3, and 4 the smallest safe system is cookie sessions plus vetted hashing, reset, and rate limiting. Writing those parts is where claim 3 bites.

**Gap:** OAuth and JWT drop out as convention. Claim 3 is a missed constraint, and it puts a maintained auth library back into the design. The tradeoff is a dependency in exchange for not owning silent security bugs.

**Flip condition:** A library forces federation or token machinery that costs more to integrate than the rebuild costs to write and review.

---
**The move that mattered:** Read the gap. Deleting the convention showed which part of it was real constraint.
**Ask yourself next time:** "Which part of the standard answer am I about to rediscover the hard way?"
**Your rep (2 min):** Name one tool you resent using. Write the one constraint it handles that you would otherwise own.

## 3. Large forecast: "AI brings universal high income in about 10 years"

**Verdict:** Unlikely on that clock. Production can rise; a universal high claim needs a distribution rule the sentence never states.

**Outcome:** By about 2036, a person who owns no machines can live well without working, in most countries.

| # | Claim | Grade | Why |
|---|---|---|---|
| 1 | AI keeps improving | measured (recent) / borrowed (10-year slope) | The observed trend, extrapolated |
| 2 | Robots reach economically relevant numbers | open | Today's fleet is thousands; the target is on the order of 10⁸ |
| 3 | Cheaper labor makes labor-heavy goods cheaper | mechanism | Price follows the binding input |
| 4 | Abundance becomes everyone's high income | open | Income is wages + ownership + transfers |
| 5 | The state allocates for order | objective | Its floor tracks the price of compliance, not of abundance |

**Bedrock:** Claim 2 is an exponent from a small base (rate × years). Claim 4 reduces to who decides (claim 5) and what that decider is optimising.

**Rebuild:** Prices fall sector by sector. Land, permits, care, and status stay scarce. The floor is whatever the state funds to keep order.

**Gap:** "High" and "universal" are tradeoffs set by politics. A short clock is borrowed optimism from one actor.

**Flip condition:** Tens of millions of reliable robots in use, plus a funded transfer near today's median income in a large economy.

---
**The move that mattered:** Name the decider. The sentence named a technology and skipped the allocator.
**Ask yourself next time:** "Who decides who gets this, and what are they optimising?"
**Your rep (2 min):** Take one headline prediction you saw this week and write who would have to decide for it to come true.
