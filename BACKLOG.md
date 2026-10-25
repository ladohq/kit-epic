# Backlog

- **kit-sdlc: an explicit target branch in the task.** `sdlc` 0.5.0 targets an epic branch
  only "when the tracker has epics and the task's epic has a branch on the remote"
  (`agents/analyst.md`). With a tracker without epics, the `epic` kit's epic-branch option
  needs the run's task to name the target branch and the analyst to take it. Until then the
  option stays unverified (README of `epic`, "Known limit"). From the triage of
  `improve/epic` (LAD-103, 2026-10-18).

- **Flow engine: a conditional gate (`gate_when`)** only if R18 via `review`'s `continue`
  proves awkward in a run. From the triage of `improve/epic-2` (LAD-145, 2026-10-25).

## epic 0.2.1

Open findings of `kit-reports/epic-0.2.0-2026-10-25.md` (commit fd61f24); the human left
them for later at `release_ok` of `improve/epic-2` (2026-10-25).

- **F2.1** [medium] `intake` does not look for the remote branch of an earlier, cancelled
  run of the same epic, where its state lives until `close`.
- **F3.3** [medium] `step-review` "At close": `readiness/<key>` is deleted even when the
  push failed, and a second `close` after a lost context has no rule when it is gone.
- **F2.2** [low] At start/resume in `intake` there is no `plan.md` or `brief.md` on the
  run's branch to `git show`: read only `decisions.md` there.
- **F3.1** [low] The base is not merged into the run's branch before `readiness` reached
  from a `delivery_ok` rejection or from `close`'s `stale`.
- **F3.2** [low] `status` is rewritten when a step's gate opens, not when the human
  answers it.
- **F3.4** [low] A resumed step's run cannot take the name `lead-sdlc` prescribes: the
  cancelled run of that name still exists.
- **F5.5** [low] `run` step 0 names "a row or the step's counter line", not the line
  `Epic: …` that `plan_ok` answers go on.
- **F5.6** [low] The blueprint lags the text: the `sdlc` version in the known limit, the
  `Epic: …` line and intake rows in R5, the `status` rebuild and "readiness check next" in
  R19.
- **F8.1** [low] After a rejection at `step_ok`, `review` must read every source again
  though only the revision is asked for.
- **F9.1** [low] `plan_ok`'s question: "It also merges" has no clear subject; say "The
  lead also merges".

Left for later at the second `release_ok` of `improve/epic-2` (report commit 3747819):

- **F5.7** [medium] `step_ok`'s "yes, create" covers only the next step, not the later
  steps its revision added (S4b after a split), which then start after `continue`.
- **F3.5** [low] Clearing `paused (waits for sdlc)` does not say what the Phase becomes
  (`steps`).
- **F3.6** [low] A pause said during `readiness` is committed at `delivery_ok` and stays in
  the closed plan; `close` step 3 does not remove it.
- **F3.7** [low] `review`'s `next` (pause) and `finish` (no step left) overlap when both
  hold.
- **F6.2** [low] `run` step 1 pushes after `continue` what `review` already pushed.
- **F9.2** [low] `review`: "the State with the pause" should name Phase `paused (waits for
  sdlc)`.
- **F2.3** [low] `flow_status(run)` does not name the workers of an ended run, and a role's
  name recurs between runs: take the names from the run's lines in `lado log`.
- **F8.2** [low, missed earlier] After `gaps`, `review` must read every step source again;
  the source then is the `delivery-report`.
- **F7.3** [medium] A "pause" said during `intake` of a new epic has no State to go to; after
  a lost context the first `review` may report `continue`. Fix: at `intake` record it as a
  row of `decisions.md`, which `plan` carries into the State.
- **F3.10** [low] With `core.logAllRefUpdates=false` the worktree's HEAD reflog is empty and
  `intake` cannot tell it already reset.
- **F5.13** [low] "the old run's" pause lines of a continued epic: wording.
- **F9.4** [low] `step-review` line 74 ("for each") is long.
- **F9.5** [low] `flows/epic.yaml` line 185 is long.
- **F2.7** [low, missed earlier] A continued epic does not put the new run in the State's
  `Epic run:`.

From `kit-reports/epic-0.2.0-2026-10-25.md` (commit 40c6183); the human released 0.2.0 with
them (gate `release_ok` of `improve/epic-2`, 2026-10-25).
