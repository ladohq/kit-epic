# Build report: epic 0.1.0 (third visit)

Commit `c2b452f` on `lado/kit-epic/create-epic`, after the critic's re-evaluation `bb23e0c`.

**Status: blocked on F3.6. It needs the human: a change to the flow skeleton in the blueprint.**

## Findings

- F3.6 `close` has one outcome, and a refused push of the readiness branch or a red CI on the closing pull request leaves the run stuck: not fixed — needs the human. The critic's fix adds an outcome `stale: readiness` to `close`. The skeleton in BLUEPRINT.md section 2 has `close: outcomes: {closed: done}`, and the flow skeletons are the human's decision. The text alone cannot fix it: once the human has approved delivery, nothing in the flow leads back to a fresh readiness check, and the lead must not fix product code or force-push. Options for the human:
  (a) add `stale: readiness` to `close` in the skeleton (`readiness` already has `max_visits: 3`);
  (b) leave the skeleton as it is and accept that the human settles this case by hand (cancel, or `lado flow-set`), with the README saying so.
  Recommendation: (a). The text in `step-review` "At close" and in `close` is unchanged for now ("tell the human; the readiness check is out of date").
- F8.1 `git switch -c readiness/<key>` fails on a later visit: fixed. `skills/step-review/SKILL.md` "At readiness" uses `git switch -C … origin/<epic branch>` (a later visit starts it again from the current epic branch). The abort path ends with `git switch <the run's branch>` before the report. "At close" deletes the branch with `git branch -D readiness/<key>`, and `flows/epic.yaml` `close` step 2 says so.
- F5.8 a continued epic loses its plan: fixed. In `flows/epic.yaml` `intake` step 1, `plan.md` keeps its done steps and numbers, and its State goes back to `plan`. The write line says "(when continuing, add to them)". `plan` says "on a continued epic, extend it, keeping its done steps and their numbers".
- F5.5 "runs it again" pointed at the lead: fixed. In `skills/epic-files/SKILL.md` "When a command fails", the lead tells `delivery` to run it again, and runs its own commands again itself.
- F5.6 conflict between the failure rule and `delivery`'s upgrade: fixed. Same section: "unless the role says what to report instead (`delivery`'s upgrade that cannot run here)".
- F5.7 "the one product file": fixed. `skills/epic-files/SKILL.md` "Changing the files" now names the release checklist, and also `delivery`'s conflict resolutions of the base merge at readiness.
- F7.1 the design_ok guard: fixed. `skills/step-brief/SKILL.md` "Starting the step's run": the first rejection counts once per epic. On a second miss the decision is recorded and the human gets two choices with a recommendation. "Switch to the base": the epic branch reaches the base by a pull request merged after green CI, State becomes `Branch: base`, the design is approved as is, and later briefs target the base. "Wait for `sdlc`": cancel the step's run (`lead-sdlc`, "At cancel"), set the step back to planned, and pause the epic in State.
- F8.2 a rejection at `plan_ok` after the third review reaches `max_visits`: fixed as the critic's first option. In `flows/epic.yaml` `plan`, the lead tells the human the next review reaches its limit and recommends `continue` when the critic should see the revision. `max_visits` is unchanged: it is in the skeleton.
- F5.2 (STILL OPEN, low) "untouched" in `epic-files`: fixed. "untouched, except the fast-forward pull `lead-sdlc` makes after a run".

Question for the human (critic, on the epic-branch option): unchanged. The option is kept with the warning in the README; the kit-sdlc task is the human's.

## Cut text

None.

## Files changed on this visit

`flows/epic.yaml`, `skills/step-review/SKILL.md`, `skills/epic-files/SKILL.md`,
`skills/step-brief/SKILL.md`. BLUEPRINT.md: no change (word counts are unchanged).

## `lado kits check .`

```
epic: OK (2 agents, 3 skills, 0 packs and 1 flows)
read-only roles: plan-critic
expects skills: tracker, sdlc-settings
expects commands: git
```

## kit-budget

```
| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 2 | 3 / 5 | green |
| Work steps in a flow | flows/epic.yaml | 7 | 5 / 8 | yellow |
| Gates in a flow | flows/epic.yaml | 3 | 2 / 3 | yellow |
| Words in a role prompt | agents/delivery.md | 539 | 800 / 1500 | green |
| Words in a role prompt | agents/plan-critic.md | 344 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 760 | 1000 / 1500 | green |
| Own skills | kit | 3 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

Similar paragraphs: none
Overall: yellow
```
Both yellow measures are justified in BLUEPRINT.md, section 4.

## Flow compare

`flow_diagram.py . --compare BLUEPRINT.md`: same as the plan in BLUEPRINT.md.
