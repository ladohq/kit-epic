# Backlog

- **kit-sdlc: an explicit target branch in the task.** `sdlc` 0.5.0 targets an epic branch
  only "when the tracker has epics and the task's epic has a branch on the remote"
  (`agents/analyst.md`). With a tracker without epics, the `epic` kit's epic-branch option
  needs the run's task to name the target branch and the analyst to take it. Until then the
  option stays unverified (README of `epic`, "Known limit"). From the triage of
  `improve/epic` (LAD-103, 2026-10-18).

## epic 0.1.1

Open findings of `kit-reports/epic-0.1.0-2026-10-18.md` (commit 9f632a5); the human released
0.1.0 with them (gate `release_ok` of `improve/epic`, 2026-10-19).

- **F3.5** [medium] `stale` does not cover a closing pull request (epic branch → base) that
  conflicts because the base moved on during `delivery_ok`.
- **F7.4** [medium] `cancel` at a pause or a limit loses commits only on the run's branch
  (the step's key, decision rows).
- **F8.4** [medium] After `continue` past the limit, "third visit → ready" in `readiness`
  is unclear, and a red pull request does not become a fix step.
- **F7.3** [medium] At the limit in `close`, nothing says when to recommend `continue` and
  when `cancel`.
- **F5.3** [medium] A design after "Switch to the base" is approved "as it is" though the
  step's branch is out of date.
- **F2.3** [medium] still open (see the report).
- Low: F3.7 ("newer side" in `plan.md`), F8.5 (resuming does not clear the pause in State),
  F6.5 (the `stale` condition repeated in `step-review`), F2.4, F6.4.
