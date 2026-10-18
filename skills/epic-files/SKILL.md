---
name: epic-files
description: >-
  The epic's files in the product repository, `docs/epics/<key>/` (brief.md, plan.md with
  its State, decisions.md, report.md), the epic's key, the readiness checklist of topics,
  how the files change (only in the epic run's worktree, merged by a plan pull request),
  what to do when a command fails, and the safety rules on the human's machine. Use when
  reading or writing any of these files, assessing a statement or a result against the
  checklist, when a push, CI or check fails, or before a command that deletes anything.
---

# The epic's files

An epic lives in the product repository, in `docs/epics/<key>/`. These files are the
epic's memory: the lead reads them at every start, so what is not in them is lost with
the lead's context. Write them in the language of the openspec documents of `.lado/sdlc.md`
(`sdlc-settings`, **Languages**).

## The key

The epic's tracker task's key, converted as `sdlc-settings` says ("The task key as a
name"), when the epic has a tracker task; any task can be the epic, whatever its type.
Without one, a short name agreed with the human at intake (`docker-install`). The key
names the folder and, on an epic branch, the branch.

## brief.md

The statement as clarified: the human's words first, quoted; then the goal and what
"done" means; the readiness checklist with a verdict per topic (covered: where; missing:
which question or step covers it; not relevant: why); the branch strategy; the epic's
tracker task, if any; the verdict, "ready to plan" or "research first" with what is
unknown.

## plan.md

```
# Plan: <feature>

## State
Phase: <intake|plan|steps|readiness|closed> · Epic run: <run> · Branch: <base | epic/...>
In work: S<n> (<KEY>, run <name>) · Next: S<n> · Open checks: S<n> until <event>
Standing rules of the human: <one line each>
Last review: <date>, base at <commit>

## Steps
### S<n> — <title> (<S|M|L>)
Status: planned | in progress (<KEY>, run <name>) | done (<KEY>, PR <link>) |
        done, check open until <event> | dropped (<reason>)
Check: <what proves it works, who runs it, where>
Touches: <files or modules>   (when it is a parallel candidate)
Notes: <leftovers carried in, decisions it rests on>

## Open questions
## Later
<item — why not now — link to a task — how it relates to the human's future plans>
```

`S<n>` numbers never change once a step is in the tracker: a split makes `S4a`, `S4b`.

## decisions.md

One row per decision, newest last; the base of the final report and of any later
autonomy, so record a decision even when it agrees with the recommendation.

| Date | Where (step, gate) | Question | Options | Recommended (by whom) | Decided (by whom) | Taken? |
|---|---|---|---|---|---|---|

## report.md

Written by `delivery`: the verdict; results of all steps (step, pull request, check,
evidence); checked by hand; upgrade from the previous version; missing; open checks and
where they went in the release checklist; the decisions table whole, with how many
recommendations were taken.

## Readiness checklist

Goal and "done" · users and how they use it · scope and what is out · data and
migrations · secrets and credentials · update and rollback · removal · platforms ·
observability (logs, errors the user sees) · tests and what only a human can check ·
documentation.

## Changing the files

- Edit and commit only in the epic run's worktree (the path `flow_status` gives), by
  path, never in the human's checkout. Besides `docs/epics/<key>/`, the lead and
  `delivery` change only the product's release checklist (the file its release docs
  name), where a moved check goes with the next plan change, and, at readiness,
  `delivery`'s conflict resolutions of the base merge (`step-review`, "Epic branch").
- The plan's changes reach the base branch as a pull request from the run's branch, always
  to the base, also on an epic branch, so the state is on the base for the next start.
  The lead pushes and opens it only after the human's yes at a gate, waits for green CI
  and merges it, with the code host and its tool from `sdlc-settings` (**Git**); when the
  base is protected, check its rulesets too, not only branch protection (with `gh`:
  `gh api repos/<owner>/<repo>/rules/branches/<base>`).
- After the plan pull request merged, delete the run's branch on the remote when the host
  did not, so no merged branch is left on the host.
  Then `git fetch origin`; only when `git log origin/<base>..HEAD` lists no commit,
  `git reset --keep origin/<base>`; otherwise `git merge origin/<base>`. A reset at any
  other time would drop commits not on the base yet (rows of `decisions.md`, statuses);
  never reset in `readiness`. On a merge conflict in the epic's files keep the rows of
  both sides (newest last) and commit; in any other file `git merge --abort` and tell the
  human.
- After a commit to a file that is also an artifact (`plan`, `epic-brief`,
  `delivery-report`), write the artifact again from the file at once. The lead starts in
  the human's checkout, so it passes `write_artifact` the absolute path in the epic run's
  worktree; a relative path would show the human an older file.

## When a command fails

First tell whether the work caused it. A red CI on a plan pull request, or a red check at
readiness, that the change itself causes (a broken link, a failing docs check, a
regression) is the work's: the lead fixes a plan change in the epic run's worktree; at
readiness it is a gap. A cause outside the work (the network, a missing tool, a refused
push or merge, branch protection, a flaky CI run, a host past its time ceiling) is no outcome and no gap, unless the step or role says what to report
instead (`close`'s `stale` for the push of the readiness branch and the pull request from
the epic branch into the base, `delivery`'s upgrade that cannot run here): `delivery`
sends the command and its whole output to the lead with `send_message` and waits; the lead shows them to the human and waits until they say it is
settled, then tells `delivery` to run it again; its own commands the lead runs again
itself.

## Safety on the human's machine

Run nothing that deletes or resets beyond what you created: no `docker system prune`,
`docker volume prune`, `docker image prune`, `rm -rf` outside your worktree or folders,
`git clean`, or `git stash` in the human's checkout. Remove your own containers, volumes
and folders by name. The human's checkout stays on its branch, untouched, except the
fast-forward pull `lead-sdlc` makes after a run, on its conditions.
