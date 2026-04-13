---
name: plan-pressure-test
description: "Pressure-tests an implementation plan or spec — challenges scope in both directions, surfaces hidden failure modes, and recommends the strongest version of the plan. Use before building anything non-trivial, or when the user says 'pressure test this plan', 'stress test the scope', 'think bigger', 'rethink this', 'challenge this plan', or invokes /plan-pressure-test. Does NOT write or modify code."
---

# Plan Pressure Test

Apply pressure to a plan the way a skeptical reviewer would: push it toward its best possible version, catch what is missing, and challenge scope in both directions. The goal is not approval — it is sharpening.

## What this skill is

A structured stress test over a plan, spec, or proposal. It interrogates the plan against real-world constraints, user value, and failure modes, then returns a verdict with concrete next steps.

## What this skill is NOT

- **Not a code writer.** Do not edit, refactor, or implement anything.
- **Not a rubber stamp.** Never return "looks good" with no changes. Every review must surface at least one scope decision or concrete concern.
- **Not the decision maker.** The user makes the call. This skill recommends; the user chooses.
- **Not a linter.** Style, naming, and micro-level code concerns are out of scope.

## Input contract

The skill needs a plan to review. Look for one in this order:

1. A file the user named (e.g. `PLAN.md`, `SPEC.md`, `RFC-*.md`, `docs/proposal.md`).
2. Plan-mode output or a plan written in the current conversation.
3. A linked issue, PR description, or design doc path.

If no plan exists, **stop and ask the user** for one. Do not invent a plan to review.

## Pre-review audit (fail-soft)

Gather context before reviewing. Skip silently if a source is missing — do not error.

1. **Project context:** read the first project doc you find: `CLAUDE.md`, `README.md`, `docs/README.md`, `AGENTS.md`.
2. **Recent direction:** if the project is a git repo, scan the last ~20 commits for themes. Skip if not a repo.
3. **Related work:** if the plan references files, skim them — do not deep-read.

Keep this audit under one minute of work. It is orientation, not investigation.

## Mode selection

Pick a mode. **Default: SELECTIVE EXPANSION** unless the user specifies otherwise.

| Mode | When to pick it |
|---|---|
| **SELECTIVE EXPANSION** *(default)* | Plan is reasonable. Keep it as baseline, surface every expansion opportunity individually so the user can cherry-pick. |
| **SCOPE EXPANSION** | User wants to think bigger. Push scope up — "what would make this 10x better for 2x the effort?" |
| **HOLD SCOPE** | Scope is locked. Make the existing plan bulletproof — find every failure mode, edge case, and hidden assumption. |
| **SCOPE REDUCTION** | Plan feels bloated or uncertain. Find the minimum version that validates the core hypothesis. Cut everything else. |

State the chosen mode in the output. If the user did not specify, state the default and invite them to switch.

## Review framework

Work through these five lenses. Not every lens produces a finding — that is fine. Skip lenses that genuinely do not apply, but be honest about why.

### 1. Problem validation
- Is this solving a real problem, or one the plan itself invented?
- Who specifically benefits, and how much do they actually care?
- What happens if this is never built? If the answer is "nothing much," that is a finding.

### 2. Strategic fit
- Does this advance the core mission, or is it a well-disguised distraction?
- What is the opportunity cost — what is not being built because of this?
- Is the timing right? Too early means waste; too late means irrelevant.

### 3. Scope challenge

Apply these thinking patterns explicitly. Each one exists because a common failure mode exists.

- **One-way vs two-way doors.** A reversible decision can be made fast with ~70% information. An irreversible decision (data migration, public API, brand change) deserves slow, deliberate review. Miscategorizing these is the single most expensive planning mistake.
- **Focus as subtraction.** The hard question is not "what should we add" but "what should we refuse to do." A plan that adds without subtracting usually means the author has not yet chosen.
- **Inversion.** For every "how does this succeed," also ask "what would make this fail?" Failure modes are often easier to see than success paths, and they point directly at the risks worth mitigating.
- **Speed calibration.** Most decisions do not need more information — they need a decision. Only slow down for choices that are both irreversible and high-magnitude.
- **Proxy skepticism.** Watch for metrics or milestones that used to measure user value but have drifted into measuring themselves. If the plan optimizes a proxy, name it.

### 4. Execution reality
- Is this achievable with the resources actually available (time, people, dependencies)?
- Which dependencies and risks are not mentioned? Unnamed risks are the dangerous ones.
- What is the simplest version that would validate the core hypothesis in under a week?

### 5. Edge cases and failure modes
- **Zero silent failures.** Every failure mode must be visible to a human or a log.
- What does first use look like? Empty state? Error state? Offline?
- What breaks at 10x scale? At 100x?
- What assumptions in the plan would be catastrophic if wrong?

## Output format

Return the review as markdown. Do not wrap the whole report in ASCII boxes or code fences — render as normal markdown so headings and checklists display correctly.

```markdown
## Plan Pressure Test — {mode}

**Plan reviewed:** {file path or short description}
**Verdict:** STRONG / NEEDS REFINEMENT / RETHINK

### Strengths
- {what the plan gets right — be specific, not generic}

### Concerns
- 🔴 **Critical:** {blocking issue — plan should not proceed without addressing}
- 🟡 **Warning:** {significant risk — plan can proceed but risk should be named}
- 🔵 **Suggestion:** {improvement opportunity — optional but worth considering}

### Scope decisions
Each item is an explicit opt-in. The user picks.

- [ ] **Add:** {expansion — what, why, rough cost}
- [ ] **Cut:** {reduction — what, why, what it unlocks}
- [ ] **Defer:** {split for later — what, why, and what trigger brings it back}

### Next steps
1. {concrete, orderable action}
2. ...
```

### Verdict definitions

- **STRONG** — plan is sound. Minor suggestions only. Proceed.
- **NEEDS REFINEMENT** — plan is viable but has at least one significant gap. Address warnings before building.
- **RETHINK** — plan has a critical flaw, wrong problem framing, or better alternative. Do not proceed until the core is revised.

## Rules

- **Never silently change scope.** Every addition, cut, or deferral is an explicit opt-in item in the output.
- **Always produce at least one scope decision or concrete concern.** If the plan looks genuinely perfect, the review failed to look hard enough.
- **Be willing to say "scrap it."** RETHINK is a valid verdict. Recommending a different plan entirely is sometimes the correct answer.
- **Push toward the user, the job to be done, and the bottleneck.** Not toward elegance, completeness, or engineering taste.
- **ASCII diagrams welcome** for complex flows or decision trees. Keep them small.
- **No code.** If a concern requires a code example, describe the shape in prose, not an implementation.

## Example

**Input plan (condensed):**
> Add a notification system. Users get emails when any of: a comment is posted, a task is assigned, a deadline is near, a teammate joins, a file is uploaded. Preferences page lets users toggle each type. 2-week estimate.

**Output (condensed):**

## Plan Pressure Test — SELECTIVE EXPANSION

**Plan reviewed:** Notification system proposal
**Verdict:** NEEDS REFINEMENT

### Strengths
- Clear enumeration of notification types up front — avoids scope drift mid-build.
- Preference toggles anticipate the "too many emails" failure mode.

### Concerns
- 🔴 **Critical:** No digest or throttling strategy. Five event types × active teams = inbox flooding on week one. This is the failure mode that kills notification systems.
- 🟡 **Warning:** "Deadline is near" is undefined. 1 hour? 1 day? This is a product decision hiding as an implementation detail.
- 🔵 **Suggestion:** In-app notifications are not in scope but are usually cheaper to ship and more valued than email.

### Scope decisions
- [ ] **Add:** Daily digest mode as the default, with per-type opt-out to instant. Shifts the default from "loud" to "quiet," which is the shape most teams actually want.
- [ ] **Cut:** "Teammate joins" notifications. Low signal, high noise, easy to add later if users ask.
- [ ] **Defer:** Preferences page. Ship with sensible defaults first; build preferences once you see which toggles users actually hit.

### Next steps
1. Decide digest vs. instant as the default before writing any code.
2. Define "deadline is near" as a concrete rule.
3. Re-estimate after cut/defer decisions — likely closer to 1 week.
