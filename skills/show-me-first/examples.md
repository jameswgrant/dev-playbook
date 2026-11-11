# Using show-me-first

Invoke the skill explicitly with a topic or question. Use the syntax supported by your agent host after registering the skill:

```text
/show-me-first Help me understand the transactional outbox pattern.
/show-me-first Explore the trade-offs between synchronous replication and availability.
/show-me-first Show me a working example of a Python closure capturing a variable.
/show-me-first Help me reason about splitting ownership of a shared service between teams.
```

In hosts that use dollar-prefixed skill invocation, use `$show-me-first` instead. Continue naturally with questions such as “Why?”, “What if the worker crashes?”, or “I know that part; explore the harder case.” There is no required lesson format or quiz.

## Making the skill available

The package follows this repository's `skills/<name>/SKILL.md` layout with supporting Markdown beside the entrypoint. Storing the package here does not guarantee automatic discovery by an agent host. Load `SKILL.md` explicitly by path, or register/copy the whole directory into the host's supported skill location. For example, without installation: “Read `skills/show-me-first/SKILL.md` and use it to explain the transactional outbox pattern.”

The package requests explicit-only invocation in two host-specific ways: `disable-model-invocation: true` in frontmatter and `policy.allow_implicit_invocation: false` in `agents/openai.yaml`. Hosts differ in which metadata they support; verify explicit-only behavior when registering the package. A host that supports neither control should load the file only on request. The package needs no particular tool, renderer, network access, or runtime.

## Why it is designed this way

- One continuing scenario reduces the effort of reconstructing context across explanations and comparisons.
- Observable behavior anchors theory, while honest labels separate executed results from predictions and hypothetical leadership outcomes.
- Flexible media and optional experiments support experienced users, deep technical work, and useful tangents without imposing a lesson template or task workflow.
- The core stays in `SKILL.md`; usage and evaluation guidance stay separate so ordinary invocation loads only relevant teaching instructions.

See [evals.md](evals.md) for six reusable evaluation cases and a rubric that values accuracy, nuance, and freedom of conversation.

## Validation scope

YAML types, invocation controls, local links, Markdown fences, and naming were checked. The standard-frontmatter validator used during authoring rejects the host-specific `disable-model-invocation` key. Its standard checks passed on a temporary copy with only that key removed; the original file was retained and its boolean checked separately. The six cases received an author walkthrough, and the Python example was executed. Independent model runs and target-host discovery/activation tests remain unperformed.
