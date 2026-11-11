# Evaluating show-me-first

Run each case in a fresh conversation with the skill explicitly loaded. Supply the initial prompt, then the follow-up. Use no additional teaching instructions. Preserve the responses and execution evidence, if any, for comparison after edits. These are behavioral checks, not exact wording or length tests.

## What counts as a good response?

Judge each dimension as **met**, **partial**, or **missed**, citing a passage or observed behavior:

- **Concrete grounding:** unfamiliar material begins with inspectable behavior, a trace, or a realistic scenario before substantial abstraction. Known material begins at the user's unresolved question.
- **Causal clarity:** explanations connect named actors, actions, and consequences; comparisons retain an accessible scenario and identify changed assumptions.
- **Accuracy and nuance:** explanations preserve the mechanism, uncertainty, failure modes, and relevant trade-offs. Predicted or hypothetical results are labeled; claimed execution has evidence.
- **Conversational freedom:** follow-ups get direct answers at the requested depth. Experiments are optional, tangents remain available, and the user is neither quizzed nor pushed into execution.
- **Presentation:** prose and optional diagrams are readable; essential explanations remain prominent. Structure serves the question rather than repeating a fixed template.

Do not reward shortness by itself. Treat a misleading technical claim, fabricated execution, ignored expertise, blocked tangent, mandatory quiz, or automatic task plan as a failure even if other dimensions are met. Record any quality lost through compression.

## 1. Learning an unfamiliar architecture concept

**Prompt:** `$show-me-first I haven't used the transactional outbox pattern. Help me understand what happens when a checkout service needs to save an order and publish OrderPlaced.`

**Follow-up:** `The relay published the event, then crashed before marking it sent. What happens now?`

Look for a concrete order, database, relay, and consumer. A context or sequence diagram is useful if readable. The explanation should expose the original dual-write failure, show the order and outbox record in one local database transaction, and connect a relay retry to possible duplicate delivery. It should distinguish reliable publication from exactly-once business effects and explain consumer idempotency with the same order.

**Failure signals:** abstraction before any event sequence; claiming the outbox alone prevents duplicates; changing examples so the crash is hard to follow.

## 2. Exploring a complex technical trade-off

**Prompt:** `$show-me-first We serve reads from two regions. Compare synchronous cross-region replication with asynchronous replication when the inter-region link fails. I want the hard trade-offs, not a recommendation yet.`

**Follow-up:** `Go deeper on quorum placement, stale reads, and the latency cost even when there is no partition.`

Look for named regions, a concrete write and subsequent read, and explicit acknowledgement and read-routing assumptions. The answer should distinguish replication from consistency guarantees, explain that quorum overlap requires suitable protocol and read behavior, and discuss latency, durability, stale reads, and partition behavior without a universal winner. The follow-up should expand technically rather than restart the introduction.

**Failure signals:** “synchronous is always consistent”; treating every replication scheme as the same protocol; flattening the answer into a slogan or insisting on a decision.

## 3. Learning from a working example

**Prompt:** `$show-me-first Use a runnable Python example to show why functions built in a loop can all return the same value, then show a fix.`

**Follow-up:** `I understand the default-argument fix. Compare it with a factory function, including a captured mutable object.`

Look for a runnable example such as `[lambda: i for i in range(3)]` yielding `[2, 2, 2]`, followed by `[lambda i=i: i for i in range(3)]` yielding `[0, 1, 2]`. Explain lookup at call time versus binding a default at function creation. A factory supplies a separate binding per invocation; neither approach inherently copies a supplied mutable object. Show how mutating a shared list remains visible and where an explicit copy would change that result. Distinguish verified output from expected output.

**Failure signals:** claiming a default argument deep-copies objects; showing only a definition of closures; forcing another beginner exercise after the user states understanding.

## 4. A software leadership decision

**Prompt:** `$show-me-first Two product teams depend on a shared service. A central team owns every change and has become a bottleneck. Help me explore splitting ownership while keeping incident response workable.`

**Follow-up:** `Both teams say the other owns an outage caused by a shared schema change. How would the ownership options play out?`

Look for a realistic change or incident with named teams, decision rights, on-call responsibilities, constraints, and stakeholder consequences. Compare plausible ownership options through that incident, separating a proposed arrangement from observed outcomes. Discuss incentives, coordination cost, and ambiguous accountability without presenting an organizational structure as a guaranteed cure.

**Failure signals:** generic leadership platitudes; replacing exploration with an assigned migration plan; unrequested implementation, messages, or confirmations; pretending a hypothetical outcome is evidence.

## 5. A topic the user already understands

**Prompt:** `$show-me-first I already understand event loops, promises, and microtasks. Skip the basics. Explain how an unbounded chain of microtasks can starve timers.`

**Follow-up:** `No demo this time. Just explain why yielding through another resolved promise does not fix the starvation.`

Look for a direct explanation at the stated level: another promise continuation schedules more microtask work, so a continuously nonempty microtask queue can delay timer tasks. Discuss the need for an appropriate task boundary in the chosen runtime without inventing a universal API. The follow-up should honor the request for prose and avoid repeating event-loop basics.

**Failure signals:** another introductory lesson or required diagram; demanding a prediction; presenting a runtime-dependent scheduling guarantee as universal.

## 6. An open-ended exploratory conversation

**Prompt:** `$show-me-first Can we explore whether a software architecture is more like a city or an ecosystem? I'm thinking aloud; I don't need an implementation plan.`

**Follow-up:** `Follow the ecosystem tangent into path dependence and power. Where does the analogy break down?`

Look for a concrete anchor, such as a shared platform used by several teams, followed by a willing exploration of the requested analogy. Connect history, incentives, and control to observable consequences while identifying limits of the metaphor. Keep a route back to the shared platform without forcing an immediate return.

**Failure signals:** refusing the tangent; treating metaphor as proof; ending with mandatory actions, a quiz, or an architecture decision.

## Checking activation separately

In the target host, verify that an ordinary learning question does not activate this skill, and that an explicit invocation does. Verify registration before testing name-based invocation. File checks can establish the presence of the invocation controls; they cannot prove a host honors those controls.

## Initial author walkthrough

The author applied the skill to all six prompt/follow-up pairs and reviewed the response samples against the five dimensions above. Each dimension was judged met in these samples, with the following evidence:

- **Architecture:** order 42 remained the anchor from the dual-write failure through relay retry and consumer idempotency; the explanation preserved the external-side-effect boundary.
- **Trade-off:** the London/New York write and read exposed acknowledgement and routing assumptions; the follow-up expanded into asymmetric majority placement and protocol requirements without selecting a winner.
- **Working example:** both binding approaches and shared mutable references were shown in executable Python. Execution confirmed `[2, 2, 2]`, `[0, 1, 2]`, shared mutation, and the effect of an explicit shallow copy.
- **Leadership:** the same schema incident tested three ownership options, including incentives and accountability. Consequences stayed hypothetical and no implementation plan was imposed.
- **Familiar topic:** the response started at starvation, explained the follow-up directly in prose, and skipped introductory material and another demonstration.
- **Exploration:** the shared platform remained available while the response followed path dependence and power, then identified where the ecosystem metaphor obscures deliberate political choices.

All samples used readable prose or code, named causes and consequences, and continued without mandatory participation. This is an author self-evaluation, not an independent model benchmark or evidence of target-host activation. Future runs should retain actual transcripts and record partial or missed dimensions rather than assuming these outcomes recur.
