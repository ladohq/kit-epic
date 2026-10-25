# Отчёт о ките: epic 0.2.0

- Дата: 2026-10-25
- Кит: корень worktree прогона `improve/epic-2` (`.lado/worktrees/kit-epic/improve-epic-2`), путь дан шагом
- Коммит: 15e0e5b
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка, шестой визит `evaluate`. Предыдущий отчёт — эта же оценка на 22d4446 (`changes`, 4 находки). Базой изменения служит 7e79334, её называет план. Правка этого визита: `git diff 22d4446`, коммит 15e0e5b.
- Проходы:
  - три прохода по правке 15e0e5b и тексту вокруг неё: проход 1 сделал критик, проходы 2 (с flow, с опытами в git) и 3 (с навыков) — свежие независимые субагенты;
  - проверку вырезанных правил критик сделал сам;
  - блокирующих находок не осталось, поэтому был полный проход по трём затронутым файлам (`flows/epic.yaml`, `skills/epic-files/SKILL.md`, `skills/step-review/SKILL.md`). Остальные файлы с полных проходов на 40e65d1 и 160a6c9 не менялись, кроме BLUEPRINT §5 (5f80dea);
  - критик сам повторил опыт с reflog в git 2.39.5;
  - находки одного прохода: отброшено 0, оставлено как подтверждённые 4.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3; обоснованы в BLUEPRINT.md, раздел 4. Промпт лида 997 слов |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 6 новых находок в изменённом тексте (0 high, 2 medium, 4 low). Без находок 9 критериев из 12. Из 4 находок прошлого визита 3 RESOLVED, F9.3 закрыта частично. Ещё 1 находка в разделе «Пропущено ранее» |
| Охват | повторная оценка правки 15e0e5b (3 файла), с полным проходом по этим 3 файлам |
| Правило остановки | не выполнено: 0 high, 2 medium. Это совет для гейта релиза, а не запрет |

Счёт относится только к тому, что названо в строке «Охват». Его нельзя сравнивать со счётом полной оценки.

**Вердикт: `approved`.** Причины:
- `lado kits check` прошёл без ошибок;
- красных мер нет, жёлтые обоснованы;
- `--compare` разницы не печатает;
- открытых находок high нет.

Находка high прошлого визита (F12.5) закрыта, и это подтверждено опытом в git.

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
| Words in the lead's prompt | agents/supervisor.md | 997 | 1000 / 1500 | green |
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

1. Строка §5 чертежа: «checked by its reflog» (то есть по reflog ветки) → «by the worktree's HEAD reflog»; добавить, что на каждую паузу — своя строка (F5.12).
2. Пауза во время `intake` нового эпика: State ещё нет, записать её некуда. Записывать строкой `decisions.md`, `plan` переносит её в State (F7.3).

## Находки

Новые находки в правке 15e0e5b. Номера продолжают прежние отчёты.

### 1. Границы ролей

Без находок.

### 2. Передача между шагами

Без находок. Нота `next`, которая цитирует строки паузы, приходит в `run` с ответом `step_ok` (`runs.py`, `_answer_note`: «Note before the gate: …»), также после `continue` на пределе визитов.

### 3. Готовность и исходы

- **F3.10** [low] `flows/epic.yaml:31` (state `intake`)
  >          <the run's worktree> reflog show HEAD` has no line `reset: moving to
  Пустой reflog читается как «сброса ещё не было». При `core.logAllRefUpdates=false` в репозитории лида git вообще не пишет reflog HEAD (проход 2 проверил: `reflog show HEAD` в новом worktree пуст после `reset --keep`). После потери контекста лид сбросит ветку ещё раз и сотрёт ответы. При настройках по умолчанию всё работает, случай редкий.
  Fix: «an empty reflog proves nothing: then reset only when `git -C <the run's worktree> log origin/<base>..HEAD -- docs/epics/` shows no commit». Passes: 1/3, confirmed (опыт прохода 2)

### 4. Независимая проверка

Без находок.

### 5. Противоречия

- **F5.12** [medium] `BLUEPRINT.md:369` (§5)
  > | 2026-10-25 | 0.2.0 | `intake` resets the run's branch once, checked by its reflog after a lost context; the human's pause is lifted only by the `step_ok` it opened | `kit-reports/epic-0.2.0-2026-10-25.md`, F12.2–F12.4, F5.8, F3.8, F5.9, F7.2 |
  Правка чертёж не трогала. «Its reflog» здесь — reflog ветки, а `flows/epic.yaml:32-33` прямо говорит, что он ненадёжен. Строки об одной строке на паузу, о цитировании нотой `next` и о сбросе пауз при продолжении эпика нет. Чертёж расходится с построенным текстом.
  Fix: в этой строке — «by the worktree's HEAD reflog»; дописать «one `Pause asked` line per pause, quoted by `next`, removed after the `step_ok` that approved it; a continued epic drops the old lines». Passes: 3/3 (проход 3 — medium; проход 2 и полный проход упомянули, не засчитав; взят medium)

- **F5.13** [low] `flows/epic.yaml:26` (state `intake`)
  >          numbers, its State back to `plan` without the old run's `Pause asked` lines.
  «The old run's» требует судить, кто написал строку. Пауза, сказанная в `intake` этого рана, по `step-review` коммитится сразу (это шаг лида), и в State её от старых отличает только дата.
  Fix: «without the `Pause asked` lines it has on the base». Passes: 1/3, confirmed

### 6. Дублирование

Без находок. Снятие паузы — только в `run` шаг 3; `step-review:76` ссылается на него.

### 7. Когда звать человека

- **F7.3** [medium] `skills/step-review/SKILL.md:73-74`
  > The human may say "pause" at any time: add a State line `Pause asked: <date and time>`
  > for each, and commit it, at once while the run is in your step or at a gate, otherwise once the run is
  У нового эпика во время `intake` нет `plan.md` и State: первый `plan.md` пишет `plan` (`epic.yaml:60`), а `epic-files:84` подтверждает «`brief.md` is written only at its end». Пауза, сказанная в `intake`, живёт только в контексте лида. После его потери первый `review` строки не увидит и отчитается `continue`, вопреки README:16 «stop at the next review». Раз `run` больше не снимает паузу после `plan_ok`, такая пауза должна дожить до первого ревью — но записать её негде.
  Fix: «in `intake`, before `plan.md` exists, record it as a row of `decisions.md`; `plan` copies it into the State». Passes: full pass, confirmed

### 8. Петли на повторном визите

Без находок.

### 9. Краткость и «почему»

- **F9.4** [low] `skills/step-review/SKILL.md:73-74`
  > The human may say "pause" at any time: add a State line `Pause asked: <date and time>`
  > for each, and commit it, at once while the run is in your step or at a gate, otherwise once the run is
  У «for each» нет множественного числа, к которому оно относится, «commit it» — в единственном. Строка 74 не переразбита (102 символа).
  Fix: «add a State line `Pause asked: <date and time>` for each pause and commit it, …», переразбить. Passes: 3/3

- **F9.5** [low] `flows/epic.yaml:185` (state `review`)
  >       `Pause asked` line (the note quotes each such line), with the revision and the next step's draft for the human's yes: note_summary is the
  Остаток F9.3: строка 143 символа при ширине файла около 90.
  Fix: переразбить абзац. Passes: 1/3, confirmed (`awk 'length>95'` — 143)

### 10. Описания навыков

Без находок.

### 11. Нейтральность к провайдеру

Без находок.

### 12. Безопасность и рамки

Без находок. Сброс в worktree рана, проверка по его reflog HEAD. Опыт критика (git 2.39.5): `worktree add -b run1 ../wt HEAD` на актуальной базе → `reset --keep origin/main` → `reflog show HEAD` печатает `HEAD@{0}: reset: moving to origin/main`, хотя ветка не сдвинулась. После коммита строка на месте (`grep -c` = 1). Reflog HEAD checkout человека не задет.

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка без учёта окружения | Закрыта: `epic-files` «First tell whether the work caused it» |
| 2. Работа вне flow, мерж без гейта | Закрыта: проверка сброса не зависит от чужих коммитов, PR плана — после `plan_ok` |
| 3. Путь вне worktree рана | Закрыта: `git -C <the run's worktree> reflog show HEAD` / `reset --keep` |
| 4. Вердикт без порога | Закрыта (без изменений) |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | Закрыта (без изменений) |
| 6. Команды не в ладу с `expects.commands` | Закрыта: только `git` |
| 7. Read-only роли не в ладу с работой | Закрыта (без изменений) |

## Не прослежено

Ничего: новых элементов нет, жёлтые меры обоснованы, `--compare` совпадает. Строка §5 чертежа отстаёт от текста — F5.12.

## Прежние находки

Отчёт на 22d4446.

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F12.5 [high] повторный сброс стирает ответы | RESOLVED | `epic.yaml:30-33` «`git -C / <the run's worktree> reflog show HEAD` has no line `reset: moving to / origin/<base>` (written even when the reset moves nothing; the branch's own / reflog is not)»; опыт выше (редкий случай с выключенным reflog — F3.10) |
| F3.9 [medium] второе «pause» | RESOLVED | `epic-files:47` «one line per "pause" of the human»; `epic.yaml:141-144` «remove a `Pause asked` / line only after `step_ok` approved a `next` whose note quoted that line … keep every line the note did not quote» |
| F5.11 [low] пауза отменённого рана | RESOLVED | `epic.yaml:26` «without the old run's `Pause asked` lines» (формулировка — F5.13) |
| F9.3 [low] длинная строка и «names» | частично | «quotes» вместо «names»; строка 185 всё ещё 143 символа (F9.5) |
| 18 находок BACKLOG «epic 0.2.1» | STILL OPEN, оставлены планом | BACKLOG |

## Вырезанные правила

Правка 15e0e5b.

| Вырезанное правило (файл:строка в 22d4446) | Где оно сейчас |
|---|---|
| `epic.yaml:30-32` проверка по `reflog show <the run's branch>` | `epic.yaml:30-33` по `reflog show HEAD`, с причиной |
| `epic.yaml:141-142` «whose note named that pause … keep a pause said at the gate itself» | `epic.yaml:141-144` «whose note quoted that line … keep every line the note did not quote (a pause said at the / gate itself or later)» |
| `epic-files:47` «Pause asked: <date>» | `epic-files:47` «Pause asked: <date and time>», одна строка на паузу |

Утерянных правил нет.

## Пропущено ранее

Находки в тексте, который diff не трогает. Вердикт они не блокируют и в правиле остановки не считаются.

- **F2.7** [low] `flows/epic.yaml:26` (state `intake`, продолжаемый эпик)
  >          numbers, its State back to `plan` without the old run's `Pause asked` lines.
  При продолжении State возвращают на `plan`, но `Epic run:` новым раном не заменяют, а поиск папки после рестарта (`agents/supervisor.md:29`: «the folder whose `plan.md` State names this run (`Epic run:`)») ищет именно по этому полю. Остаётся запасной путь через `epic-brief`. Это F2.4 отчёта `epic-0.1.0-2026-10-18.md`: она не в BACKLOG и в тексте не исправлена.
  Fix: «… its State back to `plan` with `Epic run:` this run». Passes: full pass, confirmed

## Оставлено планом

18 находок BACKLOG «epic 0.2.1», отложенных человеком на двух `release_ok`.

## Вопросы для человека

1. Два medium (F5.12 — строка чертежа, F7.3 — пауза во время `intake`) и четыре low — короткие правки. Релизить с ними или внести в BACKLOG «epic 0.2.1»? Рекомендация: F5.12 исправить до релиза — это одна строка чертежа, а чертёж служит основой следующей оценки. Остальное — в BACKLOG.

## Найдено по пути

- `[lado]` `flow_start` не принимает точку старта (`runs.py:90`). Из-за этого кит держит сброс ветки и его проверку по reflog. Параметр `base`/`from` у `flow_start` убрал бы весь этот механизм.
