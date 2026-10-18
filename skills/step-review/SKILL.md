---
name: step-review
description: >-
  How to review an epic step that ended and revise the plan: the fixed sources of a step's
  results and where each is, the plan-revision techniques with their examples, traps that
  recur between steps, a release between steps, and bringing the base into an epic
  branch. Use after a step's run of `task` ends, when a release happens mid-epic, or
  before merging an epic branch; for drafting the next step use `step-brief`.
---

# Reviewing a step

A step's run reports only that its pull request merged; what it left behind is spread
over several places, and the least visible ones hold the most (the run's talk with the
human, its leftovers). Read every source below for each step that ended, and in `retro`
say what each gave, "nothing", or "unavailable: <why>".

## The fixed sources

| Source | Where | What to take |
|---|---|---|
| openspec proposal, "Out of scope" | the archived change on the base (`openspec/changes/archive/`), or the run's `design` | deferred leftovers, the human's decisions with dates |
| ledger | the run's artifact `<run>/ledger`, and the review report it names | "Not checked", declined findings, found on the way |
| integrator's comment | the step's tracker task, with `tracker` | what to check by hand, regression risk, what is left by hand |
| the run's talk with the human | `lado log <session> --agent <worker>` for each worker of the run (`list_agents`; without `lado` on PATH, mark it unavailable), and your own messages about the run | the analyst's questions, rejections at gates, trouble in the manual check, leftovers named at the end |
| the run's artifacts | `list_artifacts(run=<run>)`: `design`, `build-report`, `ledger`, mock-ups | measured figures, decisions for the record |
| the base since the last review | `git log` of `origin/<base>` from the commit in the plan's State | changes outside the epic that touch what its steps rest on (CI, a shared file) |

## Revision techniques

Apply each that fits; name the technique in `retro`.
- **Record the result**: status, pull request, figures measured.
- **Carry leftovers** ("Not checked", "Out of scope", declined findings) into the future
  step they belong to, by source. A leftover of one step is often a requirement of a later
  one.
- **Merge** two steps when the check of one runs into the other.
- **Split** a step when its parts are independent and one blocks later steps.
- **Reorder** when a blocker is gone and the next step is worth less than the one after.
- **Change the approach** in a future step instead of patching the current one.
- **Add to "Later"** what the step moved out of its frame, with the task and how it
  relates to the human's future plans.
- **Check against the human's future plans**: a step must not close a door they named.
- **Re-check scope after a decision taken mid-way**: which steps does it add work to?
- **Compare figures**: the decisions' numbers (size, time) against the measured ones;
  correct the decisions.
- **Tasks born in the step**: put each into the plan or "Later".
- **A check possible only after an event** (a release): status "done, check open until
  <event>"; when the human's next step rests on part of it, recommend checking that part
  now, with a short instruction.
- **A step that changes the user's path** (install, update): compare the product's
  release checklist with the new path in the same review, and fix the checklist with the
  plan.

## Traps between steps

The same mistake twice (in a manual check, a review, a design) is a pattern: name it in
`retro`, put a line for it into the next step's brief, and, when the user would meet it
too, into a step of the plan (documentation, a check). Known ones: the manual check run
from the wrong branch or folder, or with variables left in the shell from an earlier test;
a design that grows round after round without the question "step or new task?".

## A release between steps

When the base gets a release (a tag, a new version in the CHANGELOG, the human says so):
1. For each open pull request of the epic, recommend bringing the base in, and check the
   CHANGELOG line sits under the version that is open on the base now.
2. Move each check open until this release into the product's release checklist, with
   what to run and what to expect, so it outlives the epic; mark it in the plan.
3. Advice the human did not take (a release before a step you recommended): record it in
   `decisions.md`, and move its risk into the release checklist as a check, instead of
   arguing.

## Epic branch

Bring the base into the epic branch in the epic run's worktree with a clean tree, never
force-pushing the epic branch.

- **After every step** (the lead, in `review`): `git fetch origin`, `git switch --detach
  origin/<epic branch>`, `git merge origin/<base>`, `git push origin HEAD:<epic branch>`,
  then `git switch <the run's branch>`. On a conflict, `git merge --abort`, switch back,
  and put the files to the human with a recommendation (a step of the plan, or leaving it
  to `delivery` at readiness).
- **At readiness** (`delivery`): `git fetch origin`, `git switch -C readiness/<key>
  origin/<epic branch>` (a later visit starts it again from the current epic branch),
  `git merge origin/<base>`; resolve conflicts as its role says and commit, or
  `git merge --abort`; check on this branch; then `git switch <the run's branch>` before
  writing the report. Nothing is pushed:
  the human sees the resolutions in `delivery-report` first.
- **At close** (the lead, after `delivery_ok`): `git push origin
  readiness/<key>:<epic branch>`, then `git branch -D readiness/<key>`. When the push is
  refused because the epic branch moved, report `stale`: the readiness check is out of
  date.
