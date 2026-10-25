# kit-epic

A [LADO](https://github.com/ladohq/lado) kit that leads a feature, an *epic*, from a
free-form statement to a delivered result, using the kit
[sdlc](https://github.com/ladohq/kit-sdlc) for every step:

1. It checks the statement against a readiness checklist and asks you what is missing.
2. It plans small verifiable steps (research first when data is missing, a walking
   skeleton early); a fresh critic reviews the plan before you see it.
3. Step by step: on your yes it creates the step's tracker task and runs it as a run of
   `sdlc`'s flow `task` in the same session; you keep `sdlc`'s design and merge gates, and
   the lead gives its recommendation on each with the epic's context.
4. After every step it reviews the results from fixed sources and revises the plan. When
   the review changed nothing of the plan, the next approved step starts at once; when it
   split or added a step, changed the next one, or has a question for you, the next step
   waits for your yes at `step_ok`. Say "pause" to the lead to stop at the next review.
5. At the end a delivery worker checks release readiness (all steps, manual checks, an
   upgrade from the previous version with old files) and writes the final report with
   the decisions that matter. Releasing stays yours.

Each epic has a short artifact `status` (phase, steps done, what waits for you, the next
action); the lead writes to you only when an epic starts, when a step ends, about once an
hour on a long step, and when the epic waits for you outside a gate.

The kit writes no product code. The plan, the decisions log and the epic's state live in
the product repository, `docs/epics/<key>/`, and reach the base branch by two pull
requests the lead merges after green CI, after `plan_ok` and at the close; in between the
lead pushes the run's branch. The lead reads them at every start and resume.

## Install and start

```bash
lado kits add https://github.com/ladohq/kit-epic.git
lado kits add https://github.com/ladohq/kit-sdlc.git
lado kits add <a tracker kit, e.g. https://github.com/ladohq/kit-tracker-yougile.git>
lado start <product repo> --kit epic --kit sdlc --kit <tracker kit>
```

Then tell the lead the feature: "epic: <statement>", from one line to a full
specification.

Requires:
- LADO 0.34 or later.
- `git` on the agents' PATH.
- The kit `sdlc` in the session: it brings the skill `sdlc-settings` (the project's
  `.lado/sdlc.md`) and the flow `task` every step runs.
- A tracker kit that brings the skill `tracker` (such as `tracker-yougile` or
  `tracker-jira-server`).

Both `epic` and `sdlc` have a supervisor, so LADO's built-in supervisor leads the session
with the lead skills `lead-epic` and `lead-sdlc`; this is intended.

The role `plan-critic` is read-only, so the kit does not run on Codex CLI.

## Flow `epic`

`intake` (lead) → `plan` (lead) → `plan_review` (plan-critic) → gate `plan_ok` →
`run` (lead: tracker task and run of `task`) → `review` (lead) → `run` when the plan is
unchanged, else gate `step_ok` → `run` …
→ `readiness` (delivery) → gate `delivery_ok` → `close` (lead).

![epic](blueprint-flows/epic.svg)

Your decisions: the open questions and the plan, which also covers starting its steps
while the plan stays as approved (`plan_ok`), each next step after a review that changed
the plan (`step_ok`), the gates of each step's run, and the delivery (`delivery_ok`).

## Changes in 0.2.0

- Fewer gates: `step_ok` only after a review that changed the plan.
- The artifact `status`, and far fewer messages from the lead.
- Two plan pull requests per epic instead of one per gate; `decisions.md` keeps rows for
  the decisions that matter and a counter for approvals as recommended.
- The plan critic blocks steps to split and checks without the way back or the docs.

Runs of `epic` started with 0.1.0 keep the 0.1.0 flow to their end: LADO runs a run on the
flow it had at its start.

## Known limit

The epic-branch option is not yet proven. The analyst of `sdlc` targets "the epic's branch when the
tracker has epics and the task's epic has a branch on the remote". For this kit any tracker
task (or none) can be the epic, so each step's brief names the target branch and the epic,
and the lead checks the target in each step's design and asks you after a second miss.
With a tracker without epics the option will likely not work until `sdlc` takes an
explicit target branch. By default every step merges into the base branch, which this does
not affect.
