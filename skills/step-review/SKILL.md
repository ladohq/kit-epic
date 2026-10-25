---
name: step-review
description: >-
  How to review an epic step that ended and revise the plan: the fixed sources of a step's
  results and where each is, the plan-revision techniques with their examples, what
  counts as a change of the plan (a gate or none), the human's "pause", traps that recur
  between steps, a release between steps, and bringing the base into an epic branch. Use
  after a step's run of `task` ends, when the human says "pause", when a release happens
  mid-epic, or before merging an epic branch; for drafting the next step use
  `step-brief`.
---

# Reviewing a step

A step's run reports only that its pull request merged; what it left behind is spread
over several places, and the least visible ones hold the most (the run's talk with the
human, its leftovers). Read every source below for each step that ended, and in `retro`
say what each gave, "nothing", or "unavailable: <why>".

## The fixed sources

| Source | Where | What to take |
|---|---|---|
| openspec proposal, "Out of scope" | the archived change on the step's target branch (`openspec/changes/archive/`), or the run's `design` | deferred leftovers, the human's decisions with dates |
| ledger | the run's artifact `<run>/ledger`, and the review report it names | "Not checked", declined findings, found on the way |
| integrator's comment | the step's tracker task, with `tracker`, or the integrator's note at `merge` | what to check by hand, regression risk, what is left by hand |
| the run's talk with the human | `lado log <session> --agent <worker>` for each worker of the run, named in `flow_status(run)` and the run's notes (`list_agents` no longer shows a finished worker; without `lado` on PATH, mark it unavailable), and your own messages about the run | the analyst's questions, rejections at gates, trouble in the manual check, leftovers named at the end |
| the run's artifacts | `list_artifacts(run=<run>)`: `design`, `build-report`, `ledger`, mock-ups | measured figures, decisions for the record |
| checked by hand | the ledger, the integrator's note, the run's talk | for a step that changed what the user sees: whether anyone ran it by hand; if nobody did, recommend a manual check to the human, with the project's recipe when the settings or docs name one |
| the epic's numbering | the step's diff (`git diff <merge commit>^1 <merge commit>`) | each added line with a step number of the plan as a word (`S1` … `Sn`, `S7b`) or `docs/epics/`, outside `docs/epics/`: the epic's numbering stays out of the product (`step-brief`), so a hit is a leftover for the next step |
| the base since the last review | `git log` of `origin/<base>` from the commit in the plan's State | changes outside the epic that touch what its steps rest on (CI, a shared file) |

## Revision techniques

Apply each that fits; name the technique in `retro`.
- **Record the result**: status, pull request, figures measured.
- **Carry leftovers** ("Not checked", "Out of scope", declined findings) into the future
  step they belong to, by source. A leftover of one step is often a requirement of a later
  one.
- **Merge** two steps when the check of one runs into the other.
- **Split** a step by the signs of `epic-files` ("plan.md"), a step the review adds too.
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
  release checklist with the new path in the same review, and fix it at once
  (`epic-files`, "Changing the files").

## What changes the plan

The human's yes at `plan_ok` covers starting the plan's steps while the plan stays as
approved: a gate on an unchanged step only asks them again (13 of 13 such gates were
approved without a word). So the review reports `continue`, and the next step starts
without `step_ok`, unless the review changed the plan. A change is any of:
- a step added, dropped, split, merged or reordered;
- the next step's frame or check changed;
- a leftover carried into the next step;
- a new question for the human, a recommended manual check among them;
- the State has a `Pause asked` line.

Statuses, "Later" and leftovers carried into later steps are not. The note of `continue`
lists each kind with "no".

The human may say "pause" at any time: add a State line `Pause asked: <date and time>`
for each, and commit it, at once while the run is in your step or at a gate, otherwise once the run is
back from `plan_review` or `readiness` (there it stops nothing). So the review sees it
after a lost context; `run` says when it is removed (step 3).

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
  origin/<epic branch>`, `git merge origin/<base>`, run the settings' **Checks** (the
  next step's workers would get a broken branch as theirs), `git push origin HEAD:<epic
  branch>`, then `git switch <the run's branch>`. On a conflict, `git merge --abort`; on a red check from the merge, push nothing; then switch
  back and put the files or the check to the human with a recommendation (a step of the
  plan, or leaving it to `delivery` at readiness).
- **At readiness** (`delivery`): `git fetch origin`, `git switch -C readiness/<key>
  origin/<epic branch>` (a later visit starts it again from the current epic branch),
  `git merge origin/<base>`; resolve conflicts as its role says and commit, or
  `git merge --abort`; check on this branch; then `git switch <the run's branch>` before
  writing the report. Nothing is pushed:
  the human sees the resolutions in `delivery-report` first.
- **At close** (the lead, after `delivery_ok`): `git push origin
  readiness/<key>:<epic branch>`, then `git branch -D readiness/<key>`; when it fails,
  `close` says when to report `stale`.
