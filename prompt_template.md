# The Fan-Out / Critic-Loop Prompt Template

A reusable prompt structure for building web and mobile apps with coding agents.
Derived from the viral "AAA first-person shooter in one prompt" prompt, but adapted
for software where "done" has to mean something measurable, not just "an agent was wowed".

Two versions below:

- **[Short form](#short-form)** — closest to the original. Good for demos, prototypes, throwaway fun.
- **[Full form](#full-form)** — what you should actually use for real projects.

---

## What makes the original prompt work

Strip out the game specifics and seven mechanics remain. The template just re-fills each one
with a web/mobile equivalent.

| Mechanic in the original | What it's doing | Web/mobile equivalent |
|---|---|---|
| "at the level of the most recent Call of Duty" | Names an **external reference standard** instead of an adjective | Name real apps: Linear, Stripe Dashboard, Things 3, Cash App |
| "from textures to physics to anything you could think of" | Enumerates **quality dimensions** so none get skipped | States, a11y, perf budgets, responsiveness, error handling |
| "Fan out sub-agents … tackle each one individually" | **Parallel decomposition** into independent units | Workstreams split by surface/feature, not by layer |
| "/loop on each item" | **Iteration** rather than one-shot generation | Same — but with an iteration cap |
| "a separate sub-agent check it visually" | **Independent verification** — the builder doesn't grade itself | Separate reviewer agent + screenshots + automated gates |
| "a really harsh critic" | Biases the reviewer toward **rejection**, defeating agreeableness | Same, plus a written rubric so "harsh" is specific |
| "compare them side by side blind and say which one looks better" | An **objective-ish test** with a pass/fail answer | Screenshot diff vs. reference; Lighthouse/axe scores; tests |
| "Don't stop until…" | **Stop condition** | Replaced by an explicit Definition of Done + budget cap |

The one thing to change: the original's stop condition is a vibe ("utterly wowed"). For a game
demo that's fine. For an app you're shipping, it's an open-ended token furnace that can loop
forever on subjective disagreement. Everywhere the original says "until it's perfect", this
template says "until every box in the Definition of Done is checked, or N rounds, whichever
comes first."

---

## Short form

Copy, fill the `<angle brackets>`, paste.

```
Build <APP: one sentence — what it is and who it's for>.

Quality bar: it should feel like <REFERENCE APP 1> and <REFERENCE APP 2> — that level of
polish on every surface. Interaction design, motion, empty/loading/error states, typography,
spacing, accessibility, perceived speed. Nothing placeholder, nothing half-wired.

Stack: <STACK>. <ANY HARD CONSTRAINTS>.

Break this into independent workstreams and fan out a sub-agent per workstream. For each one,
loop until it's done, and have a *separate* sub-agent review the result as a harsh critic —
it should screenshot the actual running UI, compare it against <REFERENCE APP> screenshots,
and reject anything that looks like a generic AI-generated app. If it rejects, the builder
fixes and resubmits.

Done means: <3–6 CONCRETE, CHECKABLE CRITERIA>. Every one must be verified against the running
app, not asserted. Stop after <N> review rounds per workstream and report anything still failing
instead of looping forever.
```

---

## Full form

```
# PROJECT: <NAME>

## 1. Mission
<One sentence: what this is, who uses it, what job it does for them.>

Primary user journey (must work end to end before anything else is polished):
<Step 1 → step 2 → step 3 → outcome>

## 2. Quality bar
This should stand next to <REFERENCE APP 1> and <REFERENCE APP 2> without looking amateur.
Those are the benchmark. When making a judgment call, ask "what would <REFERENCE APP 1> do here?"

Explicitly, that means:
- **Visual**: consistent type scale and spacing system, real content not lorem ipsum,
  no default-framework look, light and dark themes both deliberate.
- **Interaction**: every action has immediate feedback; optimistic updates where safe;
  no dead clicks; keyboard support on every interactive element.
- **States**: every view has designed loading, empty, error, and partial/offline states.
  Empty states teach the user what to do next.
- **Motion**: purposeful transitions, <200ms, respects `prefers-reduced-motion`.
- **Responsive**: works at <BREAKPOINTS / DEVICE SIZES>. No horizontal scroll, no clipped text.
- **Accessibility**: <TARGET, e.g. WCAG 2.2 AA>. Labeled controls, visible focus, real contrast,
  screen-reader-sane structure.
- **Performance**: <BUDGETS, e.g. LCP < 2.0s on mid-tier mobile, bundle < 250KB gz,
  60fps scroll, cold start < 1.5s>.
- **Robustness**: network failures, slow connections, empty API responses, and bad input
  are all handled visibly and recoverably.
- **Data & security**: <AUTH MODEL, WHAT'S SENSITIVE, WHAT MUST NEVER LEAVE THE DEVICE/SERVER>.

## 3. Stack and constraints
- Platform/framework: <STACK>
- Backend/data: <BACKEND>
- Must use: <REQUIRED LIBS/SERVICES>
- Must NOT use: <BANNED — e.g. no new state libs, no UI kit, no paid APIs>
- Existing code and conventions to follow: <PATHS / STYLE GUIDE / CLAUDE.md>

## 4. Scope
In scope: <FEATURE LIST>
Out of scope (do not build, do not stub elaborately): <NON-GOALS>
If you think something out of scope is essential, say so and stop — don't silently add it.

## 5. Fan out
Split the work into independent workstreams and run a sub-agent per workstream. Split by
user-facing surface, not by layer — each agent should own a feature end to end so no
agent is blocked waiting on another's half.

Suggested split (adjust if you see a better one, and tell me why):
- <WORKSTREAM 1 — e.g. auth + onboarding>
- <WORKSTREAM 2 — e.g. main list/detail experience>
- <WORKSTREAM 3 — e.g. settings + account>
- <WORKSTREAM 4 — e.g. design system: tokens, primitives, motion — build this FIRST,
  others depend on it>

Shared contracts (types, API schema, design tokens, routing) get locked down before fan-out.
Agents may not unilaterally change a shared contract — propose it back to me.

## 6. The critic loop
For each workstream, run this cycle:

1. **Build** — implement the workstream against the quality bar above.
2. **Self-check** — run the automated gates (section 7). Do not submit for review while red.
3. **Review** — a *separate* sub-agent, which did not write the code, reviews it. This agent's
   job is to reject. It must:
   - run the app and drive the real UI (browser automation / simulator), not read the code
     and imagine the result;
   - screenshot every state: default, loading, empty, error, dark mode, narrow viewport;
   - put those screenshots next to <REFERENCE APP> screenshots of the equivalent screen and
     say which looks more professional and specifically why;
   - grade against the rubric in section 8 and write findings as a numbered list with
     file:line references and severity;
   - assume it will find problems. "Looks good" with no findings is only acceptable when
     accompanied by evidence of what was actually checked.
4. **Fix** — the builder addresses every blocker and either fixes or explicitly declines each
   non-blocker with a reason.
5. **Repeat** until zero blockers, or <N, e.g. 3> rounds have passed.

Budget: stop at <N> rounds per workstream. Anything unresolved at that point gets reported to
me as a known issue with the reviewer's findings attached. Do not loop indefinitely on
subjective disagreement — escalate it.

## 7. Automated gates (these are not optional and not self-reported)
Every one must actually be run, with output shown:
- `<BUILD COMMAND>` — clean, no warnings
- `<TYPECHECK COMMAND>` — zero errors
- `<LINT COMMAND>` — zero errors
- `<TEST COMMAND>` — all pass; new code has tests for the real logic, not smoke tests
- `<A11Y CHECK, e.g. axe / accessibility scanner>` — zero critical/serious
- `<PERF CHECK, e.g. Lighthouse CI, bundle analyzer>` — meets the budgets in section 2
- Manual pass of the primary user journey in section 1, screenshotted

If a gate can't run, say so explicitly. Never report a gate as passing without its output.

## 8. Review rubric
The reviewer scores each area PASS / FAIL with evidence. Any FAIL is a blocker.
- Journey: primary flow completes without confusion or dead ends
- Visual: consistent with the design system; indistinguishable in quality from <REFERENCE APP>
- States: loading/empty/error all designed and reachable
- Responsive: correct at all target sizes
- A11y: keyboard-only completion of the primary journey; contrast and labels pass
- Perf: budgets met on the specified device profile
- Robustness: failure paths recover; nothing crashes or hangs silently
- Code: fits existing conventions; no dead code, no commented-out blocks, no TODO stubs
  presented as finished

## 9. Definition of done
- [ ] Primary user journey works end to end against real (not mocked) <BACKEND>
- [ ] All gates in section 7 green, with output shown
- [ ] Reviewer signed off every workstream with zero blockers
- [ ] <PROJECT-SPECIFIC CRITERION>
- [ ] <PROJECT-SPECIFIC CRITERION>
- [ ] Known issues and deliberate omissions listed in the final report

## 10. Report back
Give me: what was built, per-workstream review verdicts, gate output, screenshots of the
primary journey, everything you decided not to do and why, and the top <3> things you'd
fix next with more time.
```

---

## Worked example

The template filled in for a small real project, so you can see the shape.

```
# PROJECT: Ledger — shared expense tracker (web)

## 1. Mission
A web app for housemates to log shared expenses and see at a glance who owes whom.
Primary journey: sign in → add an expense and split it → see updated balances → settle up.

## 2. Quality bar
Stand it next to Splitwise and Linear. Splitwise for the domain clarity, Linear for the craft.
- Visual: 4px spacing grid, one type scale, deliberate dark mode, real names/amounts in all
  screenshots and fixtures.
- Interaction: adding an expense updates balances optimistically; every button shows pending
  state; full keyboard support in the add-expense dialog.
- States: every view designed for loading, empty ("No expenses yet — add your first"),
  and error (with retry).
- Responsive: 360px → 1440px. Mobile is the primary target.
- A11y: WCAG 2.2 AA. Primary journey completable keyboard-only.
- Perf: LCP < 2.0s on simulated mid-tier mobile; JS bundle < 200KB gzipped.
- Data: balances must be correct to the cent under concurrent edits; never show a stale balance
  as if it were fresh.

## 3. Stack and constraints
Next.js App Router + TypeScript + Tailwind. Supabase for auth and Postgres.
Must not: add a component library, add a client state library beyond React Query.
Follow the conventions in CLAUDE.md.

## 4. Scope
In: auth, groups, add/edit/delete expense, split evenly or by exact amounts, balances, settle-up.
Out: receipt OCR, multi-currency, notifications, native apps.

## 5. Fan out
1. Design system + app shell (tokens, primitives, nav, theming) — build first
2. Auth + group creation/joining
3. Expense CRUD + splitting
4. Balances + settle-up
Shared contracts: DB schema, TS types in src/types, design tokens. Locked before fan-out.

## 6. The critic loop
Builder → gates → separate reviewer agent drives the real app in a browser, screenshots every
state at 360px and 1440px in both themes, compares against Splitwise screenshots of the
equivalent screen, grades on the section 8 rubric. Max 3 rounds per workstream.

## 7. Automated gates
npm run build · npx tsc --noEmit · npm run lint · npm test · axe on every route ·
Lighthouse mobile · manual screenshotted pass of the primary journey.

## 8–9. Rubric and DoD
As template. Project-specific DoD:
- [ ] Balances provably correct: property test over random expense sequences, sum of all
      balances is always zero
- [ ] Deleting an expense correctly reverses its effect on every affected balance
- [ ] No route shows a bare spinner for more than 300ms without a skeleton
```

---

## Anti-patterns

Things that make this template stop working:

- **"Perfect" with no rubric.** Without section 8, agents converge on "looks good to me" fast.
- **Reviewer that reads code instead of running the app.** The whole point of the original
  prompt is that a critic *looked at the pixels*. A reviewer that reasons about the code will
  approve a screen that renders as a blank page.
- **Builder reviewing its own work.** It will pass itself. The reviewer must be a separate agent
  with no stake in the code.
- **No iteration cap.** "Don't stop until perfect" against a subjective bar means it never stops.
- **Fan-out by layer** (one agent for the frontend, one for the backend, one for tests). They
  block each other and integrate badly. Split by user-facing surface.
- **Fan-out before contracts are fixed.** Parallel agents inventing incompatible types is the
  single most common way this pattern produces a mess.
- **No reference app.** "Beautiful" is unfalsifiable; "looks like Linear" is checkable.
- **Skipping the design-system workstream.** If it isn't built first, every parallel agent
  invents its own spacing and colors, and the app looks stitched together.
