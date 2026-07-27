# Promptly

A prompt template for building **web and mobile apps** with coding agents, adapted from the
viral "build a AAA first-person shooter in one prompt" prompt.

The original prompt got a lot of attention because it produced a genuinely impressive demo from
a few sentences. What made it work wasn't the enthusiasm — it was seven specific mechanics:
a named external quality benchmark, an enumeration of quality dimensions, parallel fan-out,
iteration, an *independent* critic, an objective comparison test, and a stop condition.

[`prompt_template.md`](./prompt_template.md) keeps all seven and re-fills them for software
that has to actually work: real acceptance criteria, real automated gates, and an iteration cap
so the loop terminates.

## How to use it

**1. Pick a form.** The template has a short form (~8 lines, closest to the original — good for
prototypes and demos) and a full form (~10 sections — use this for anything you'll maintain).

**2. Fill every `<angle bracket>` before you send it.** Unfilled placeholders are where agents
start improvising. The three that matter most:

- **Reference apps.** Name two real, shipping products. "Beautiful, modern UI" is unfalsifiable
  and gets you the default Tailwind look. "Like Linear" is something a reviewer can hold a
  screenshot against. Pick apps you can actually screenshot for comparison.
- **Definition of done.** 3–6 things that are true or false, not "high quality". If you can't
  check it by running something, it doesn't belong on the list.
- **Non-goals.** Ambitious prompts cause scope inflation. Writing down what *not* to build is
  the cheapest edit you'll make.

**3. Lock the shared contracts before fan-out.** Types, API schema, design tokens, routing.
Parallel agents that each invent their own `User` type produce a merge you'll spend the
afternoon untangling. Build the design-system workstream first and let the others depend on it.

**4. Let it run, then read the review verdicts, not the summary.** The builder's summary is
optimistic by construction. The reviewer's findings and the gate output are the real signal.

**5. Treat the iteration cap as real.** When a workstream hits the cap with open blockers, that
is information — usually that the spec was ambiguous or the bar was set somewhere the agent
can't reach unaided. Read the findings and re-scope rather than raising the cap.

## Where this differs from the original prompt

| Original | Here | Why |
|---|---|---|
| "Don't stop until utterly perfect" | Definition of done + max N rounds | Subjective bars don't terminate. Games have a visual finish line; a CRUD app doesn't. |
| Critic judges by eye alone | Critic judges by eye **and** typecheck/tests/axe/Lighthouse | Apps fail in ways screenshots don't show. Both halves are needed. |
| One quality axis (does it look AAA) | Nine axes incl. a11y, perf, error states, data correctness | "Looks great, crashes on empty state" is the standard failure mode of pretty-app prompts. |
| Unbounded fan-out | Fan out by user-facing surface, after contracts are fixed | Layer-based splits block each other; contract-free splits don't integrate. |

## Cost warning

This pattern is deliberately expensive. Parallel agents plus multi-round critic loops can burn
through a lot of tokens and wall-clock time — that's the trade for the quality bar. Use the
short form for exploration and save the full form for work that justifies it. The iteration cap
in section 6 is your main cost control; set it to 1–2 for a first pass on something new.

## Files

- [`prompt_template.md`](./prompt_template.md) — the template (short form, full form, a worked
  example, and the anti-patterns that break it)

## License

MIT — use it, fork it, change it.
