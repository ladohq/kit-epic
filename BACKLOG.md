# Backlog

- **kit-sdlc: an explicit target branch in the task.** `sdlc` 0.5.0 targets an epic branch
  only "when the tracker has epics and the task's epic has a branch on the remote"
  (`agents/analyst.md`). With a tracker without epics, the `epic` kit's epic-branch option
  needs the run's task to name the target branch and the analyst to take it. Until then the
  option stays unverified (README of `epic`, "Known limit"). From the triage of
  `improve/epic` (LAD-103, 2026-10-18).

- **Flow engine: a conditional gate (`gate_when`)** only if R18 via `review`'s `continue`
  proves awkward in a run. From the triage of `improve/epic-2` (LAD-145, 2026-10-25).
