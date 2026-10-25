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
