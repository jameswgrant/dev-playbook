---
name: show-me-first
description: Explores concepts through concrete behavior, observable consequences, and experiments when explicitly invoked for learning or discussion.
disable-model-invocation: true
---

# Show me first

Help the user understand with less mental reconstruction. Prefer:

**Experience → Observe → Understand why → Experiment → Apply**

These are observed presentation preferences, not a diagnosis or a claim about fixed learning styles. This skill guides explanation and exploration; it does not manage execution or change global agent behavior. Apply it when explicitly requested, respecting the user's current intent and existing knowledge.

## What can the user observe?

Start unfamiliar material with one concrete scenario and something inspectable: working code, input/output, a state change, a realistic decision, or a short sequence of events. Give just enough context to identify the actors, constraints, and question. Prefer supplied code and systems over an invented example when available.

Choose the medium that exposes the behavior:

- Software: actual code, runtime output, observable state, or a failure and its consequence.
- Architecture: a context diagram for actors and boundaries, a sequence diagram for order and interactions, or a concrete request trace and failure scenario.
- Leadership: a realistic decision with stakeholders, constraints, and consequences; distinguish assumptions from outcomes that have actually been observed.

Use diagrams when they reduce reconstruction. Plain prose or a small trace may be clearer. Keep labels readable, name components, and distinguish facts from assumptions. Label predicted output, simulated behavior, and hypothetical scenarios honestly; claim execution only when the example was run.

## Why did that happen?

Attach explanations to specific observations: name the component, action, and consequence. For example: “The worker crashed after committing the payment but before acknowledging the message, so the queue delivered the message again.” Introduce terminology and theory where they explain the result.

Use short paragraphs and descriptive, purpose-led headings when headings help: “What's happening?”, “Why did the worker receive the message twice?”, “What breaks?”, or “What changes?”. Choose headings for the actual question; these examples are not a required response template. Keep essential explanations at normal readable size and contrast, beside the behavior they explain rather than hidden in captions or collapsed detail. Brevity alone is not the goal.

Maintain an accessible reference to the scenario as complexity grows: retain named actors and values, refer back to the original snippet or trace, or give a compact recap after a substantial detour. Identify what changed and what stayed fixed when comparing variants. Avoid restating the entire setup every turn.

## What could change?

When useful, offer a natural experiment: predict a result, change a variable, introduce a failure, compare alternatives, or apply the idea to the user's system. Let the user explain an observed result in their own words if they want to check their understanding.

Make participation optional. Continue explaining without waiting for an answer unless missing information actually prevents progress. A thought experiment can be enough; invoking this skill does not authorize changes to a repository or a real system.

## Where does the exploration lead?

Treat the preferred sequence as a starting strategy, not a checklist for every reply. Follow the user's questions into theory, edge cases, alternatives, and trade-offs. Explain where the example stops generalizing; retain uncertainty, competing constraints, and genuinely difficult details.

Infer familiarity from the conversation. When the user already understands the foundation, start at the unresolved behavior or trade-off and build on it. Answer direct questions directly; skip demonstrations and experiments that add no understanding. Welcome useful tangents and thought experiments, keeping a route back to the original scenario. Let application emerge when relevant rather than steering every discussion toward tasks or next actions.

For invocation examples and packaging notes, see [examples.md](examples.md). Read [evals.md](evals.md) when evaluating or revising the skill; it is not part of an ordinary learning conversation.
