---
name: step-brief
description: >-
  How to draft an epic step for the human's yes and hand it to its run of `task`: the
  tracker task's draft, the brief with the step's frame, check, thresholds, target branch
  and the human's standing rules, two steps at once, and the run's start. Use when
  drafting the next step, starting its run, or checking a step's draft; for reviewing a
  step that ended use `step-review`.
---

# A step's brief

A step is one tracker task, done by a run of the flow `task` of `sdlc`. Its analyst knows
nothing of the epic except what the brief says, so the brief carries the epic's context
that the step needs, and the rules the human set for every step.

## The artifact `step-brief`

Written whole each time, in the settings' chat language, for the human's yes at the gate:
for each step it proposes (one; two only on the rules below), the tracker task's draft and
the brief.

**The tracker task's draft.** Title; the description: what the user can do after the
step, the check, the link to the epic's plan (`docs/epics/<key>/plan.md`, step `S<n>`);
the epic's tracker task to link to, when there is one. No design: that is the analyst's.

**The brief**, in this order:
1. **Step.** "Step S<n> of epic <key>: <title>." The goal of the epic in one line.
2. **Frame.** What is in, what is out (with where it went: a later step, "Later"), the
   leftovers of earlier steps carried into this one, by source.
3. **Check.** How the step proves it works, and who runs it.
4. **Thresholds.** Every conditional decision as a number the worker can measure ("add X
   unless the image grows over 1 GB or the build over 3 min"), and what to do past it.
5. **Target branch.** The base branch, or the epic branch:
   "Target branch: `<epic branch>`; the task's epic is `<key>`; the branch is on the
   remote." The analyst of `sdlc` targets "the epic's branch when the tracker has
   epics and the task's epic has a branch on the remote"; the brief names both, since
   for this kit any task can be the epic.
6. **Standing rules**, always, word for word in meaning:
   - The pull request carries the manual check from the start: which branch to switch to,
     from which folder, the commands, the expected result, and `env | grep <prefix>` and
     `unset` for variables a previous test may have left in the shell.
   - At every widening of the frame, ask the human: "is this still the step, or a new
     task?"
   - A task born in the step is created by the step, on the human's yes, linked to this
     task as derived, with the design artifacts attached.
   - The plan and the epic's files under `docs/epics/` are the epic's: leave them as they
     are.
   - The epic's numbering stays out of the product: no step numbers (`S6`), no "not built
     yet (step S6)" and no links to `docs/epics/` in its code, docs, specs or UI; say what
     is missing in the product's own words.
   - At the end, name the leftovers: what was not checked, what was declined, what is
     out of scope.
   - No global destructive command on the human's machine (`epic-files`, "Safety on the
     human's machine"); the human's checkout is never switched.
   - The human's own standing rules from the plan's State.

## Two steps at once

Only when the human asked for it and the plan marks both as candidates whose `Touches`
do not overlap. Say in each brief that the other runs alongside.

## Starting the step's run

After the tracker task exists, start the run of `task` with `flow_start`, as `lead-sdlc`
says ("Starting a run"), with the task text: the tracker key, then the brief whole, so the
analyst reads it as the human's words about the task. On an epic branch, check each
step's `design` at `design_ok`: its target branch must be the epic branch. The first time
in the epic a design names the base, recommend rejecting with the brief's line 5 quoted.
When a design names the base again, `sdlc` does not take the epic branch on this tracker:
record it in `decisions.md` and put the human two choices, with your recommendation:
- **Switch to the base**: on their yes, the epic branch reaches the base by a pull request
  you merge after green CI; set the plan's State to `Branch: base` and record it; this
  design is approved as it is, and later briefs target the base.
- **Wait for `sdlc`**: cancel the step's run as `lead-sdlc` says ("At cancel"), set the
  step back to "planned (<KEY>, paused)" and set the plan's Phase to `paused (waits for
  sdlc)` until `sdlc` takes an explicit target branch.
