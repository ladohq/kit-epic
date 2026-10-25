# Отчёт о ките: epic 0.2.0

- Дата: 2026-10-25
- Кит: корень worktree прогона `improve/epic-2` (`.lado/worktrees/kit-epic/improve-epic-2`), путь дан шагом
- Коммит: 40e65d1
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка, третий визит `evaluate`. Предыдущий отчёт — эта же оценка на fd61f24 (`approved`), плюс пункты плана. Базой изменения служит 7e79334, её называет план: `git diff 7e79334 -- kit.yaml README.md BLUEPRINT.md agents flows skills`.
- Правки этого визита: `git diff fd61f24`, коммиты cee0885, 0fb0280, edcfbae, 40e65d1. Это правки после отказа на `release_ok` и раздел «epic 0.2.1» в BACKLOG.
- Проходы:
  - три прохода по этим правкам и тексту вокруг них: проход 1 сделал критик, проходы 2 (с flow, по сценариям) и 3 (с навыков, затем роли и чертёж) — свежие независимые субагенты;
  - проверку вырезанных правил критик сделал сам;
  - блокирующих находок не осталось, поэтому затем был полный проход по всем 10 файлам текста кита. Его сделал отдельный субагент, у которого были оба прежних отчёта и BACKLOG «epic 0.2.1»;
  - каждую находку критик подтвердил в файлах, где нужно — в исходниках LADO;
  - находки одного прохода: отброшена 1 (неполное предложение в `step-review:75`, на поведение не влияет), оставлено как подтверждённые 3.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3; обе меры обоснованы в BLUEPRINT.md, раздел 4. Промпт лида 995 слов (зелёный, у границы 1000) |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 9 новых находок в изменённом тексте (0 high, 3 medium, 6 low). Без находок 7 критериев из 12. Прошлый визит: 7 находок RESOLVED, 10 оставлены планом (BACKLOG «epic 0.2.1»). Ещё 1 находка в разделе «Пропущено ранее» |
| Охват | повторная оценка изменённого текста, с полным проходом по 10 файлам diff |
| Правило остановки | не выполнено: 0 high, 3 medium. Это совет для гейта релиза, а не запрет |

Счёт относится только к тому, что названо в строке «Охват». Его нельзя сравнивать со счётом полной оценки.

**Вердикт: `approved`.** Причины:
- `lado kits check` прошёл без ошибок;
- красных мер нет, жёлтые обоснованы;
- `--compare` разницы не печатает;
- открытых находок high нет — ни в изменённом тексте, ни в «Пропущено ранее».

### `lado kits check .`

```
epic: OK (2 agents, 3 skills, 0 packs and 1 flows)
read-only roles: plan-critic
expects skills: tracker, sdlc-settings
expects commands: git
```

Expects: `expects skills: tracker, sdlc-settings`, `expects commands: git`.

Read-only: `read-only roles: plan-critic`; `dependencies.lado: ">=0.34"`.

### Скрипт бюджета (код выхода 0)

```
# Complexity budget: epic 0.2.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 2 | 3 / 5 | green |
| Work steps in a flow | flows/epic.yaml | 7 | 5 / 8 | yellow |
| Gates in a flow | flows/epic.yaml | 3 | 2 / 3 | yellow |
| Words in a role prompt | agents/delivery.md | 594 | 800 / 1500 | green |
| Words in a role prompt | agents/plan-critic.md | 376 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 995 | 1000 / 1500 | green |
| Own skills | kit | 3 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Flows

![epic](epic-0.2.0-2026-10-25/epic.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

Вердикт не блокируют. Решает человек: до релиза или в 0.2.1.

1. Сброс в `intake` после потери контекста может стереть уже закоммиченные ответы. Условие «Before the first commit» нужно сделать проверяемым через git (F12.2).
2. `run` снимает «Pause asked» при каждом старте шага. Пауза, сказанная на `plan_ok` или `step_ok`, исчезает тем же одобрением, вопреки README «stop at the next review» (F5.8).
3. «yes, create» от `step_ok` покрывает только следующий шаг. Шаги, добавленные той же ревизией, после `continue` попадают под правило `sdlc-settings`, и R18 ломается для каждого разбитого шага (F5.7).

## Находки

Новые находки в изменённом тексте. Номера продолжают отчёт на fd61f24: его id остались в BACKLOG «epic 0.2.1».

### 1. Границы ролей

Без находок. Сброс и коммит паузы — в worktree рана и в шагах лида; `delivery` и критика правки не трогают.

### 2. Передача между шагами

- **F2.3** [low] `skills/step-review/SKILL.md:27` (state `review`)
  > | the run's talk with the human | `lado log <session> --agent <worker>` for each worker of the run, named in `flow_status(run)` and the run's notes (`list_agents` no longer shows a finished worker; without `lado` on PATH, mark it unavailable), and your own messages about the run | the analyst's questions, rejections at gates, trouble in the manual check, leftovers named at the end |
  Для закончившегося рана `flow_status(run)` воркеров не называет (`runs.py`, `describe`: поле `acting` — только у активного рана, поля воркеров нет). Имена воркеров к тому же повторяются: `_next_name` снова даёт голое имя роли, когда прежний воркер удалён. Поэтому `--agent analyst` смешивает аналитиков всех шагов. Совет к F2.1 полной оценки 0.1.0 — и мой тоже — был неточен.
  Fix: «the workers' names from the run's lines in `lado log <session>`; a role's name recurs in every step, so keep only this run's time span». Passes: full pass, confirmed

### 3. Готовность и исходы

- **F3.5** [low] `flows/epic.yaml:138-139` (state `run`)
  >          remove a pause from the State (the line `Pause asked`, or Phase `paused (waits
  >          for sdlc)`), and commit.
  Снимая Phase `paused (waits for sdlc)`, текст не говорит, на что её поменять: Phase остаётся без значения.
  Fix: «… or Phase `paused (waits for sdlc)`, back to `steps`». Passes: 2/3

- **F3.6** [low] `skills/step-review/SKILL.md:75`
  > back from `plan_review` or `readiness` (there it stops nothing). So the review sees it
  Пауза, сказанная во время `readiness`, коммитится на `delivery_ok`, и потом её никто не снимает: `run` больше не будет, а `close` шаг 3 (`epic.yaml:244`) не трогает её. `Pause asked` уходит в закрытый план на базе, и эпик, который потом продолжат, её унаследует.
  Fix: в `close` шаг 3 — «remove the line `Pause asked`», или после `ready` паузу не записывать. Passes: 1/3, confirmed

- **F3.7** [low] `flows/epic.yaml:179` (state `review`)
  >       kind of change with "no". Report `next` otherwise, always when the State has `Pause asked`,
  Условие пересекается с `finish` (`:182`: «Report `finish` when the plan has no step left»), когда шагов не осталось, а пауза есть: `next` приведёт на `step_ok` без шага, `finish` проигнорирует паузу.
  Fix: «`finish` when no step is left, a pause included (it stops nothing there)». Passes: full pass, confirmed

### 4. Независимая проверка

Без находок. Сброс локален и не пушится; PR плана по-прежнему ждёт `plan_ok`.

### 5. Противоречия

- **F5.7** [medium] `flows/epic.yaml:131-132` (state `run`)
  >          (`plan_ok` is the explicit "yes, create" for the task of each step of the plan it
  >          approved, `step_ok` for the step it approves; for any other task the rule of
  `review` отчитывается `continue` для шага, одобренного «at `plan_ok` or an / earlier `step_ok`» (`:177-178`). Но «yes, create» от `step_ok` — только для «the step it approves», то есть для следующего шага. Шаги, которые добавила ревизия этого гейта (S4b после разбиения S4), после `continue` попадают в «any other task», и для них действует правило `sdlc-settings` «explicit "yes, create" for that very action». Один лид создаст задачу, другой спросит человека вне гейта. Разбиения частые (7 шагов стали 14), значит R18 ломается часто. Вопрос `step_ok` (`:192`) об этом тоже молчит.
  Fix: «`step_ok` for each step of the revision it approves»; в вопрос `step_ok` — «and the later steps of this revision start without `step_ok` while reviews do not change the plan». Passes: 2/3 (проход 3 и полный проход)

- **F5.8** [medium] `flows/epic.yaml:138` (state `run`)
  >          remove a pause from the State (the line `Pause asked`, or Phase `paused (waits
  `run` шаг 3 снимает паузу при каждом старте шага, в том числе после `plan_ok` и после `continue`. `step-review:76` говорит, что её снимают, «when the human approves the next step». Пауза, сказанная на `plan_ok`/`step_ok` («одобряю, но после этого шага — пауза») или пока `run` стартует шаг после `continue`, стирается без гейта. Это противоречит `README.md:16`: «Say "pause" to the lead to stop at the next review.»
  Fix: снимать `Pause asked` только после `step_ok`, который открыл `review` из-за неё (пауза старше этого ревью); паузу до `plan` держать строкой в `decisions.md`, `plan` переносит её в State. Passes: 2/3 (уровни low и medium, взят medium)

### 6. Дублирование

- **F6.2** [low] `flows/epic.yaml:124-125` (state `run`)
  >          request to the base branch, wait for green CI and merge it; after `step_ok` or
  >          `continue`, only push the run's branch (`epic-files`, "Changing the files").
  `review` теперь пушит ветку рана на каждом исходе (`:164`: «and the run's branch on every outcome»). Пуш в `run` после `continue` пушит то же самое ещё раз. Вреда нет, но правило записано в двух местах.
  Fix: в `run` шаг 1 пушить только после `step_ok` (там шаг 0 коммитит ответ). Passes: 1/3, confirmed

### 7. Когда звать человека

Без находок. F7.1 закрыта: `supervisor.md:100` «**"Pause"**: record it as `step-review` says ("What changes the plan").», и в описании `step-review` есть «when the human says "pause"».

### 8. Петли на повторном визите

Без находок в изменённом тексте. Петля и предел `run` не менялись. Возврат из `gaps` — в «Пропущено ранее».

### 9. Краткость и «почему»

- **F9.2** [low] `flows/epic.yaml:168` (state `review`)
  >       the step back to "planned"), write `retro` and the State with the pause, and write
  Старая формулировка, а видов паузы теперь два: лид может записать здесь `Pause asked` вместо Phase `paused (waits for sdlc)`.
  Fix: «and the State's Phase `paused (waits for sdlc)`». Passes: 1/3, confirmed

### 10. Описания навыков

Без находок. В описании `step-review` есть триггер паузы, пересечения со `step-brief` нет.

### 11. Нейтральность к провайдеру

Без находок. Только `git` и инструменты LADO.

### 12. Безопасность и рамки

- **F12.2** [medium] `flows/epic.yaml:27-28` (state `intake`)
  >          Before the first commit, put the run's branch on the base: `git reset --keep
  >          origin/<base>` in the run's worktree, the one reset of the run's branch. LADO
  Шаг 4 коммитит ответы каждого раунда (`:38`: «run's worktree, so a lost context loses no answer.»), а в `intake` их никто не пушит. Лид, потерявший контекст в `intake`, снова получает этот текст, и ничто не говорит ему, был ли уже первый коммит. На чистом дереве `reset --keep` молча уберёт ответы из ветки (останутся только в reflog). Если checkout человека не на базе, `git log origin/<base>..HEAD` покажет коммиты в обоих случаях, свои от чужих не отличить.
  Fix: «only while `git log origin/<base>..HEAD -- docs/epics/` shows no commit; never after». Passes: 3/3 (проходы 2, 3 и полный проход)

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка без учёта окружения | Закрыта: `epic-files:160` «First tell whether the work caused it» |
| 2. Работа вне flow, мерж без гейта | Закрыта: сброс внутри `intake` и локален; PR плана — после `plan_ok`, PR checklist назван в вопросе `plan_ok`. «yes, create» для шагов ревизии — F5.7 |
| 3. Путь вне worktree рана | Закрыта: «`git reset --keep origin/<base>` in the run's worktree»; коммит паузы ждёт, пока worktree у `delivery` или критика (`step-review:73-75`); повторный сброс — F12.2 |
| 4. Вердикт без порога | Закрыта (без изменений): `epic.yaml:92-93`, «Blocking» в `plan-critic.md`, «**A gap** is» в `delivery.md` |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | Закрыта (без изменений) |
| 6. Команды не в ладу с `expects.commands` | Закрыта: новая команда только `git` |
| 7. Read-only роли не в ладу с работой | Закрыта (без изменений): `plan-critic` read-only, `>=0.34`, README о Codex CLI |

## Не прослежено

Ничего: новых элементов нет, жёлтые меры обоснованы, `--compare` совпадает. В §1 чертежа записано одно исключение для сброса и его причина (`BLUEPRINT.md:173-175`), в §5 — строка 2026-10-25 об этих правках; с построенным текстом совпадает. Прежнее отставание чертежа (F5.6) оставлено планом.

## Прежние находки

Отчёт на fd61f24.

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F7.1 [medium] пауза не доходит до лида | RESOLVED | `supervisor.md:100` «**"Pause"**: record it as `step-review` says»; `step-review:8` «when the human says "pause"» |
| F5.1 [medium] коммит паузы в `readiness` | RESOLVED | `step-review:74-75` «at once while the run is in your step or at a gate, otherwise once the run is / back from `plan_review` or `readiness`» |
| F5.2 [medium] два смысла «paused» | RESOLVED | `epic-files:45` `paused (waits for sdlc)`, `:47` «Pause asked: <date>»; `epic.yaml:166` «When the epic waits for `sdlc`» (остаток формулировки — F9.2) |
| F5.3 [medium] «yes, create» после `continue` | RESOLVED для шагов плана | `epic.yaml:131-133`; для шагов ревизии `step_ok` — F5.7 |
| F5.4 [medium] нет пуша на `next` | RESOLVED | `epic.yaml:164` «and the run's branch on every outcome» |
| F12.1 [high] «Пропущено ранее»: ран от HEAD человека | RESOLVED | `epic.yaml:27-30` сброс на `origin/<base>`; `supervisor.md` «`intake` puts its branch on the base» (условие сброса — F12.2) |
| F2.1 [medium] intake и ветка отменённого рана | STILL OPEN, оставлена планом | BACKLOG «epic 0.2.1» |
| F3.3 [medium] «Пропущено ранее»: push/delete в «At close» | STILL OPEN, оставлена планом | BACKLOG «epic 0.2.1» |
| F2.2, F3.1, F3.2, F3.4, F5.5, F5.6, F8.1, F9.1 [low] | STILL OPEN, оставлены планом | BACKLOG «epic 0.2.1»; тексты не менялись (например, `epic.yaml:105` «It also merges a change of» — F9.1) |

## Вырезанные правила

Правки cee0885 и 0fb0280.

| Вырезанное правило (файл:строка в fd61f24) | Где оно сейчас |
|---|---|
| `supervisor.md:45` «The human gives a feature, from one line to a full specification.» | описание flow `epic` («from one line to a full specification») и README |
| `supervisor.md:48` «Start it from the base branch.» | `epic.yaml:27-30` сброс на `origin/<base>`; `supervisor.md` «`intake` puts its branch on the base» |
| `epic.yaml:157-158` «the run's branch `run` pushes, but on `finish` bring the base into it» | `epic.yaml:164-165` «and the run's branch on every outcome, on `finish` after bringing / the base into it» |
| `step-review:66` «the human said "pause"» | `step-review:68` «the State has `Pause asked`» |
| `step-review:71-73` «Set the State's Phase to `paused (asked <date>)` and commit at once … `run` clears it when the human approves the next step» | `step-review:73-76` (строка `Pause asked`, коммит с условием); снятие — `epic.yaml:138` (расходится — F5.8) |
| `epic-files:149-150` «Never reset the run's branch» без исключений | `epic-files:149-152` «except once in `intake` before its first commit» |

Утерянных правил нет.

## Пропущено ранее

Находки в тексте, который diff не трогает. Вердикт они не блокируют и в правиле остановки не считаются.

- **F8.2** [low] `flows/epic.yaml:175` (state `review`, визит после `readiness` → `gaps`)
  >       Done when every source is read or marked unavailable with the reason, and the files
  После `gaps` ни один шаг только что не закончился (`:157`: «Review the step(s) that just ended»), но условие готовности всё равно требует прочитать каждый источник. Лид перечитывает источники прошлого шага или помечает все «unavailable». Это другой путь, чем F8.1 из BACKLOG (возврат после отказа на `step_ok`).
  Fix: «after `gaps`, the source is the `delivery-report`; read a step's sources only for a step that ended since». Passes: full pass, confirmed

## Оставлено планом

Человек отложил их на `release_ok` прогона `improve/epic-2` (2026-10-25). Все записаны в BACKLOG.md, раздел «epic 0.2.1»:
- medium: F2.1, F3.3;
- low: F2.2, F3.1, F3.2, F3.4, F5.5, F5.6, F8.1, F9.1.

## Вопросы для человека

1. Три medium этого круга (F12.2, F5.8, F5.7) — короткие правки в `intake`, `run` шаг 3 и вопросе `step_ok`. Исправить до релиза или отложить в BACKLOG «epic 0.2.1»? Рекомендация: F12.2 — до релиза: это одно условие, а без него сброс может стереть ответы человека. F5.8 и F5.7 можно отложить.

## Найдено по пути

- `[lado]` `flow_start` не принимает точку старта: ран всегда начинается от текущего HEAD репозитория лида (`runs.py:90`), а киты пишут «Start the run from the base branch» (`lead-sdlc`). Кит `epic` теперь обходит это сбросом в `intake`; параметр `base`/`from` у `flow_start` закрыл бы это для всех китов.
- `[lado]` `flow_status(run)` у закончившегося рана не называет его воркеров, а имена воркеров повторяются между ранами (`runtime._next_name`). Связать лог воркера с раном можно только по времени (F2.3).
