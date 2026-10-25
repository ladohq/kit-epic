---
name: epic-files
description: >-
  The epic's files in the product repository, `docs/epics/<key>/` (brief.md, plan.md with
  its State and the rule for splitting a step, decisions.md, report.md), the artifact
  `status`, the epic's key, the readiness checklist of topics, how the files change (only
  in the epic run's worktree, two plan pull requests), what to do when a command fails,
  and the safety rules on the human's machine. Use when reading or writing any of these
  files or `status`, assessing a statement or a result against the checklist, when a
  push, CI or check fails, or before a command that deletes anything.
---

# The epic's files

An epic lives in the product repository, in `docs/epics/<key>/`. These files are the
epic's memory: the lead reads them at every start, so what is not in them is lost with
the lead's context. Write them in the language of the openspec documents of `.lado/sdlc.md`
(`sdlc-settings`, **Languages**). An artifact written from a file (`epic-brief`, `plan`,
`delivery-report`) is the file as it is; notes, messages to the human and `status` are in
the chat language.

## The key

The epic's tracker task's key, converted as `sdlc-settings` says ("The task key as a
name"), when the epic has a tracker task; any task can be the epic, whatever its type.
Without one, a short name agreed with the human at intake (`docker-install`). The key
names the folder and, on an epic branch, the branch.

## brief.md

The statement as clarified: the human's words first, quoted; then the goal and what
"done" means; the readiness checklist with a verdict per topic: "covered by S<n>, Check:
<its line>", "missing: <the question or the step that will cover it>", or "not relevant:
<why>" (intake has no steps yet; `plan` turns each "missing" into its step); the branch
strategy; the epic's tracker task, if any; the verdict, "ready to plan" or "research
first" with what is unknown.

## plan.md

```
# Plan: <feature>

## State
Phase: <intake|plan|steps|paused (<why>)|readiness|closed> · Epic run: <run> · Branch: <base | epic/...>
In work: S<n> (<KEY>, run <name>) · Next: S<n> · Open checks: S<n> until <event>
Standing rules of the human: <one line each>
Last review: <date>, base at <commit>

## Steps
### S<n> — <title>
Status: planned | planned (<KEY>, paused) | in progress (<KEY>, run <name>) |
        done (<KEY>, PR <link>) | done, check open until <event> | dropped (<reason>)
Check: <what proves it works, who runs it, where>
Touches: <files or modules>   (when it is a parallel candidate)
Notes: <leftovers carried in, decisions it rests on>

## Open questions
## Later
<item — why not now — link to a task — how it relates to the human's future plans>
```

`S<n>` numbers never change once a step is in the tracker: a split makes `S4a`, `S4b`.

A step is one run of `task`. Split it when it has more than one capability the user will
see, changes a data schema or file format together with the interface, has parts that can
merge separately each leaving the product working, or cannot be designed in one round of
the analyst's questions: such steps split at design anyway, after the human approved them
(7 planned steps became 14 in one epic). A step's Check names the way back when it changes
a data schema or file format (rollback to the previous version, what stays, how it is
checked), and "the docs describe what was built" when it changes what the user sees, so
the release side is checked step by step, not first at readiness.

## decisions.md

The base of the final report and of any later autonomy: it shows where the human went
another way than recommended. Newest last.
- A row for a recommendation the human did not take, a decision that changed the plan, a
  step's frame, the concept or the order, and a rejection at a gate.
- One counter line per step for the approvals as recommended, with their gate and
  question IDs: `S3: gates #40, #41, #43 — approved as recommended`.
- Not recorded: questions about the process ("can it run in parallel", "where are we"),
  unless the human confirms an answer as a rule; then it is a row, and the rule goes into
  the State.

Record: the human chose a bind mount against the recommended volume (a row). Do not
record as a row: `design_ok` of S3 approved as recommended (`#41` on S3's counter line).

| Date | Where (step, gate) | Question | Options | Recommended (by whom) | Decided (by whom) | Taken? |
|---|---|---|---|---|---|---|

## report.md

Written by `delivery`: the verdict; results of all steps (step, pull request, check,
evidence); checked by hand; upgrade from the previous version; missing; open checks and
where they went in the release checklist; the decisions table whole, with how many
recommendations were taken (rows and counter lines).

## status

An artifact only, never a file: `<run>/status` of the epic run, at most about 25 lines:
the phase; steps done N of M; the step in work with its run and that run's state; what
waits for the human, and where (the run and its gate, or the question); the open checks
until release; the next action. The lead writes it whole, rebuilt from the plan's State
and `flow_status`, at every move of the epic run and at every start and end of a step's
run. A file would need a commit at every move; what must survive a lost context is in the
State already.

## Readiness checklist

Goal and "done" · users and how they use it · scope and what is out · data and
migrations · secrets and credentials · update and rollback · removal · platforms ·
observability (logs, errors the user sees) · tests and what only a human can check ·
documentation.

## Changing the files

- Edit and commit only in the epic run's worktree (the path `flow_status` gives), by
  path, never in the human's checkout. Besides `docs/epics/<key>/`, the lead and
  `delivery` change only the product's release checklist (the file its release docs
  name) and, at readiness, `delivery`'s conflict resolutions of the base merge
  (`step-review`, "Epic branch").
- The plan's changes reach the base by a pull request from the run's branch, always to the
  base, also on an epic branch, and only twice: in `run` after `plan_ok`, and in `close`.
  After `step_ok` and after `continue` the lead only pushes the run's branch
  (`git push origin HEAD:<the run's branch>`), so no commit lives on this machine alone;
  a pull request for a few lines of markdown runs the product's full CI. So the state of
  an open epic is on its run's branch on the remote, and on the base after `close`.
- A change of the release checklist must reach a release between steps, so it goes to the
  base at once by its own pull request: `git fetch origin`, `git switch -c <the run's
  branch>-checklist origin/<base>`, commit only that change, push, then `git switch <the
  run's branch>`. `delivery`'s commit at readiness goes with the closing pull request.
- Open the plan's pull request only after the human's yes at a gate. Wait for green CI on
  each pull request and merge it with a merge commit, never squash or rebase, with the code host and its tool from `sdlc-settings` (**Git**); when the base
  is protected, check its rulesets too, not only branch protection (with `gh`:
  `gh api repos/<owner>/<repo>/rules/branches/<base>`). Then delete its branch on the
  remote when the host did not; after `close`, the epic branch too.
- After the plan pull request of `run` merged: `git fetch origin`, `git merge
  origin/<base>` (a fast-forward). Never reset the run's branch: a commit not on the base
  yet (a row of `decisions.md`, a status) would be lost.
- After a commit to a file that is also an artifact (`plan`, `epic-brief`,
  `delivery-report`), write the artifact again from the file at once. The lead starts in
  the human's checkout, so it passes `write_artifact` the absolute path in the epic run's
  worktree; a relative path would show the human an older file.

## When a command fails

First tell whether the work caused it. A red CI on a plan pull request, or a red check at
readiness, that the change itself causes (a broken link, a failing docs check, a
regression) is the work's: the lead fixes a plan change in the epic run's worktree; at
readiness it is a gap. A cause outside the work (the network, a missing tool, a refused
push or merge, branch protection, a flaky CI run, a host past its time ceiling) is no
outcome and no gap, unless the step or role says what to report instead (`close`'s
`stale`, as `close` says; `delivery`'s upgrade that cannot run here): `delivery` sends
the command and its whole output to the lead with `send_message` and waits; the lead
shows them to the human and waits until they say it is settled, then tells `delivery` to run it again; its own commands the lead runs again
itself.

## Safety on the human's machine

Run nothing that deletes or resets beyond what you created: no `docker system prune`,
`docker volume prune`, `docker image prune`, `rm -rf` outside your worktree or folders,
`git clean`, or `git stash` in the human's checkout. Remove your own containers, volumes
and folders by name. The human's checkout stays on its branch, untouched, except the
fast-forward pull `lead-sdlc` makes after a run, on its conditions, and the commit of
`.lado/sdlc.md` that `lead-sdlc` makes there with the human's yes.
