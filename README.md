# plan-pressure-test

A Claude Code skill that pressure-tests implementation plans and specs before you build. It challenges scope in both directions, surfaces hidden failure modes, and returns a structured verdict with concrete next steps.

It does **not** write code. It reviews.

## Quick install

One line — paste into Claude Code or your shell, then restart Claude Code:

```bash
git clone https://github.com/commodore65/plan-pressure-test ~/.claude/skills/plan-pressure-test
```

The skill is then available as `/plan-pressure-test` or via trigger phrases like *"pressure test this plan"*.

## What it does

Given a plan, spec, or proposal, the skill runs a structured review pass across five lenses:

1. **Problem validation** — is this solving a real problem?
2. **Strategic fit** — does this advance the mission, or is it a distraction?
3. **Scope challenge** — one-way vs two-way doors, focus as subtraction, inversion, speed calibration, proxy skepticism.
4. **Execution reality** — is this achievable with the resources actually available?
5. **Edge cases and failure modes** — what breaks, when, and silently?

It returns a verdict (`STRONG` / `NEEDS REFINEMENT` / `RETHINK`), explicit strengths and concerns, and a list of **opt-in** scope decisions the user can cherry-pick.

## When to use it

- Before building anything non-trivial.
- When a plan feels bloated and you want to find the minimum version.
- When a plan feels thin and you want to find what is missing.
- When you want a second pass on scope without touching code.

Trigger phrases: *"pressure test this plan"*, *"stress test the scope"*, *"think bigger"*, *"rethink this"*, *"challenge this plan"*, or `/plan-pressure-test`.

## Modes

| Mode | When to pick it |
|---|---|
| **SELECTIVE EXPANSION** *(default)* | Plan is reasonable. Surface every expansion opportunity individually so you can cherry-pick. |
| **SCOPE EXPANSION** | Push scope up — "what would make this 10x better for 2x the effort?" |
| **HOLD SCOPE** | Scope is locked. Make the existing plan bulletproof. |
| **SCOPE REDUCTION** | Find the minimum version that validates the core hypothesis. Cut everything else. |

## Usage

Point the skill at a plan:

```
/plan-pressure-test PLAN.md
```

Or describe the plan in chat and invoke it:

```
Here's my plan: [...]
/plan-pressure-test
```

If no plan is provided, the skill will ask for one — it will not invent a plan to review.

## Example

**Input plan:**
> Add a notification system. Users get emails when: a comment is posted, a task is assigned, a deadline is near, a teammate joins, a file is uploaded. Preferences page lets users toggle each type. 2-week estimate.

**Output:**

> ## Plan Pressure Test — SELECTIVE EXPANSION
>
> **Verdict:** NEEDS REFINEMENT
>
> ### Concerns
> - 🔴 **Critical:** No digest or throttling strategy. Five event types × active teams = inbox flooding on week one.
> - 🟡 **Warning:** "Deadline is near" is undefined. This is a product decision hiding as an implementation detail.
> - 🔵 **Suggestion:** In-app notifications are not in scope but are usually cheaper to ship and more valued than email.
>
> ### Scope decisions
> - [ ] **Add:** Daily digest as the default, with per-type opt-out to instant.
> - [ ] **Cut:** "Teammate joins" notifications. Low signal, high noise.
> - [ ] **Defer:** Preferences page. Ship sensible defaults first.

See [`SKILL.md`](SKILL.md) for the full review framework.

## Philosophy

The skill is built around a few opinions:

- **Never silently change scope.** Every addition, cut, or deferral is an explicit opt-in item the user picks.
- **Always produce at least one concern or scope decision.** If a review comes back empty, it did not look hard enough.
- **"Scrap it" is a valid verdict.** Recommending a different plan entirely is sometimes the correct answer.
- **Push toward the user, the job to be done, and the bottleneck.** Not toward elegance or engineering taste.

## License

MIT — see [LICENSE](LICENSE).
