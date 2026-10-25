# Отчёт о ките: epic 0.2.0

- Дата: 2026-10-25
- Кит: корень worktree прогона `improve/epic-2` (`.lado/worktrees/kit-epic/improve-epic-2`), путь дан шагом
- Коммит: e5f06cb
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка, второй визит `evaluate`. Предыдущий отчёт — эта же оценка на 3fa45cc (`changes`, 15 находок), плюс пункты плана. Базой изменения служит 7e79334, её называет план: `git diff 7e79334 -- kit.yaml README.md BLUEPRINT.md agents flows skills`. Правки этого визита: `git diff 3fa45cc`, коммиты 8a48e03 и e5f06cb.
- Проходы:
  - три прохода по правкам 8a48e03 и e5f06cb и тексту вокруг них: проход 1 сделал критик, проходы 2 (с flow, по сценариям) и 3 (с навыков, затем роли и чертёж) — свежие независимые субагенты;
  - проверку вырезанных правил критик сделал сам;
  - блокирующих находок не осталось, поэтому затем был полный проход по каждому файлу, который трогает diff от 7e79334 (это все 10 файлов текста кита). Его сделал отдельный субагент, критик подтвердил каждую находку в файлах и в исходниках LADO;
  - находки одного прохода: отброшено 0, оставлено как подтверждённые 2; находки полного прохода подтверждены все.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3; обе меры обоснованы в BLUEPRINT.md, раздел 4. Промпт лида 995 слов (зелёный, у границы 1000) |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 12 находок в изменённом тексте: 0 high, 6 medium, 6 low. Без находок 6 критериев из 12. Из 15 находок прошлого визита все 15 RESOLVED. Ещё 4 находки в разделе «Пропущено ранее» |
| Охват | повторная оценка изменённого текста (10 файлов diff), с полным проходом по этим 10 файлам |
| Правило остановки | не выполнено: 0 high, 6 medium. Это совет для гейта релиза, а не запрет |

Счёт относится только к тому, что названо в строке «Охват». Его нельзя сравнивать со счётом полной оценки.

**Вердикт: `approved`.** Причины:
- `lado kits check` прошёл без ошибок;
- красных мер нет, жёлтые обоснованы;
- `--compare` разницы не печатает;
- в изменённом тексте нет открытых находок high.

Единственная high лежит в тексте, который не менялся с 0.1.0 (раздел «Пропущено ранее»), и вердикт не блокирует. Она вынесена в «Вопросы для человека».

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

Вердикт не блокируют. Это советы на 0.2.1 или до релиза — решает человек.

1. Правило «пауза» человека: добавить отсылку в роль лида или триггер в описание `step-review`. Сейчас лид может не загрузить это правило, и пауза потеряется с контекстом (F7.1).
2. «paused» в Phase значит две разные вещи: паузу человека и эпик, ждущий `sdlc`. После потери контекста лид может пойти не по той ветке `review` (F5.2).
3. Пауза «at any time» требует коммита и в `readiness`, где worktree занят `delivery` (F5.1).
4. На `next` ветку рана не пушат до одобрения `step_ok`. Эпик на паузе может ждать неделями, и коммиты всё это время живут только на этой машине (F5.4).
5. Создание задач после `continue` без показа черновика человеку противоречит правилу `sdlc-settings` «yes, create for that very action». Нужно назвать исключение явно (F5.3).

## Находки

Только находки в изменённом тексте.

### 1. Границы ролей

Без находок. Права лида: `agents/supervisor.md:15-17` («the product's release checklist … and, on an epic branch, the base merges of»). Слияния базы в ветку рана — в собственных шагах лида (`review` на `finish`, `close`). `delivery` «push[es] nothing».

### 2. Передача между шагами

- **F2.1** [medium] `flows/epic.yaml:20-21` (state `intake`)
  > 1. Read the statement, the code and docs it touches, and `docs/epics/` of
  >    `origin/<base>` after `git fetch origin`: an epic of the same feature may exist already. Then ask the human
  До `close` новое состояние эпика лежит только в ветке его рана на remote (`epic-files:130-131`: «So the state of / an open epic is on its run's branch on the remote»), а после `cancel` остаётся только там (`epic.yaml:255`). Ран, продолжающий такой эпик, расширяет старые файлы с базы: строки `decisions.md` теряются, а для шага, чей статус изменился в старой ветке, может появиться вторая задача.
  Fix: «and the remote branch of an earlier run of it (`Epic run:` in its State); continue from that branch when it is ahead of the base». Passes: full pass, confirmed

- **F2.2** [low] `agents/supervisor.md:33`
  >    worktree is on another commit. Before `plan`, read `brief.md`.
  Новый поиск папки в `intake` её находит, но предложение выше по-прежнему велит читать `plan.md` из ветки. В `intake` нет ни `plan.md`, ни `brief.md` (`epic-files`: «`brief.md` is written only at its end»). `git show` падает, и лид может принять это за сбой команды.
  Fix: «In `intake`, read only `decisions.md`». Passes: 2/3

### 3. Готовность и исходы

- **F3.1** [low] `skills/epic-files/SKILL.md:143`
  >   fast-forward), before `readiness` (in `review`, on `finish`) so `delivery` moves checks
  В `readiness` ведут ещё два пути мимо `review`: `delivery_ok` → `rejected` и `close` → `stale`. На этих визитах названная причина не выполняется, и `delivery` работает со старым checklist. Слияние в `close` шаг 3 потом это поправит, работа не теряется.
  Fix: «before every `readiness` (in `review` on `finish`; in `close` before reporting `stale`)». Passes: 1/3, confirmed

- **F3.2** [low] `skills/epic-files/SKILL.md:109`
  > step's run, and at every gate or question of a step's run. A file would need a commit at every move; what must survive a lost context is in the
  `status` пересобирают, когда гейт рана шага открылся, но не когда человек на него ответил. До следующего события `status` говорит, что гейт ждёт человека. Об ответе лиду сообщает LADO: `runs.py`, `_tell_supervisor(after, f"human answered {word} at {found.state}")`.
  Fix: «at every gate or question of a step's run and its answer». Passes: 1/3, confirmed

### 4. Независимая проверка

Без находок. Ни гейт, ни ревью не убраны. PR checklist ждёт зелёного CI. Перед `plan_ok` по-прежнему read-only критик, перед `delivery_ok` — свежий `delivery`.

### 5. Противоречия

- **F5.1** [medium] `skills/step-review/SKILL.md:71-72`
  > The human may say "pause" at any time. Set the State's Phase to `paused (asked <date>)`
  > and commit at once, so the review sees it after a lost context; `run` clears it when the
  Противоречит `agents/supervisor.md:102-103`: «Change the worktree only while / the run is in your step or at a gate, not in `readiness`, where `delivery` has it.». В `readiness` (worktree на `readiness/<key>` или на detached `origin/<base>`) и в `plan_review` (read-only критик) лид закоммитит в чужой рабочей копии. В `readiness` пауза к тому же ничего не останавливает.
  Fix: «commit at once while the run is in your step or at a gate; otherwise once it is back». Passes: 3/3 + full pass

- **F5.2** [medium] `flows/epic.yaml:160` (state `review`)
  >       with `step-brief` and write `step-brief` whole. When the epic is paused (`step-brief`,
  `paused (<why>)` (`epic-files:45`) теперь означает и эпик, ждущий `sdlc` (`step-brief`: «pause the epic in the plan's State until»), и паузу человека (`step-review:71`: «`paused (asked <date>)`»). После потери контекста лид, прочитав паузу человека, может пойти по этой ветке: напишет «No step proposed … approving resumes S<n> with its tracker task <KEY>» для несуществующего шага и скажет, что `step_ok` ждёт `sdlc`.
  Fix: держать паузу человека отдельной строкой State (`Pause asked: <date>`) или назвать вариант `sdlc` как `paused (waits for sdlc)`. Passes: 2/3

- **F5.3** [medium] `flows/epic.yaml:127` (state `run`)
  >          (the human's yes at `plan_ok` or `step_ok` is the yes to create), linked to the
  `sdlc-settings:70-71` — правило «Writing to the tracker», которое побеждает даже Extra instructions: «Creating a new task … needs the human's / explicit "yes, create" for that very action; having shown them a draft or asked». После `continue` черновик шага человек не видел. Один лид создаст задачу, другой спросит человека, и тогда R18 не работает.
  Fix: назвать исключение явно: «`plan_ok` is that explicit yes for each plan step's task; `sdlc-settings`' rule still holds for any other task». Passes: full pass, confirmed

- **F5.4** [medium] `flows/epic.yaml:157-158` (state `review`)
  >       `step-review` here; the run's branch `run` pushes, but on `finish` bring the base
  >       into it and push it here (`epic-files`, "Changing the files").
  На `next` ничего не пушится, пока `step_ok` не одобрен. Эпик на паузе (ждёт `sdlc`) может стоять на `step_ok` неделями, отказ тоже возвращает без пуша. Коммиты ревью (строки решений, пауза) всё это время живут только на этой машине, вопреки `epic-files:129` («so no commit lives on this machine alone;») и R10 чертежа. F5.1 прошлого визита исправлен только для `finish`.
  Fix: «push the run's branch here on every outcome». Passes: full pass, confirmed

- **F5.5** [low] `flows/epic.yaml:116-117` (state `run`)
  >       0. After a gate, record the answer in `decisions.md` (`epic-files`: a row or the
  >          step's counter line) and change `plan.md` where it changes it; commit. After
  После `plan_ok` по новому правилу ответ идёт на строку `Epic: …` (`epic-files:81-82`), а здесь — «the step's counter line». Лиды запишут `plan_ok` то на строку S1, то на `Epic:`.
  Fix: «a row, the step's counter line or the line `Epic:`». Passes: 2/3

- **F5.6** [low] `BLUEPRINT.md:325`
  > Known limit to verify when building: `sdlc` 0.5.0 targets an epic branch only "when the
  Чертёж отстаёт от построенного текста:
  - версия `sdlc` выше (в `step-brief` и README её уже нет);
  - в R5 нет строки `Epic: …` и правила «at intake every answer is a row»;
  - в R19 нет пересборки «at every gate or question of a step's run» и строки «readiness check next»;
  - в строке `epic.plan_ok` нет PR checklist.
  Fix: убрать версию, дописать эти пункты в R5, R19 и строку `plan_ok`. Passes: 2/3 (проход 3 и полный проход)

### 6. Дублирование

Без находок. Правило разбиения теперь одно (`step-review:39`: «**Split** a step by the signs of `epic-files` ("plan.md"), a step the review adds too.»). Правило слияния базы — в `epic-files`, flow на него ссылается.

### 7. Когда звать человека

- **F7.1** [medium] `skills/step-review/SKILL.md:7-8`
  >   release between steps, and bringing the base into an epic branch. Use after a step's
  >   run of `task` ends, when a release happens mid-epic, or before merging an epic branch; for drafting the next step use `step-brief`.
  Правило паузы есть только в `step-review`, а в описании навыка нет триггера «человек сказал pause». В `agents/supervisor.md` слово «pause» не встречается (`grep -n -i pause agents/supervisor.md` — пусто). Пока идёт шаг, лид может не загрузить правило и держать паузу только в контексте. После рестарта `review` отчитается `continue` вопреки слову человека. F7.1 прошлого визита исправлен не до конца.
  Fix: строка в «When the human asks» в роли лида со ссылкой на `step-review`, «What changes the plan», или триггер в описании навыка. Passes: 3/3 + full pass

### 8. Петли на повторном визите

Без находок. Текст о пределе `run` совпадает с LADO: `runs.py`, `_enter` (каждый вход сверх предела снова открывает гейт) и `_answer_note` («Note before the gate: …»); «act on the note before that limit» исполнимо.

### 9. Краткость и «почему»

- **F9.1** [low] `flows/epic.yaml:101-102` (gate `plan_ok`)
  >       change, or your "pause" to the lead, brings `step_ok`. It also merges a change of
  >       the product's release checklist by its own pull request at once, so a release
  У «It» нет ясного подлежащего: читается как «одобрение» или «изменение», а не как лид. Между тем именно это предложение покрывает мерж в базу (`epic-files:133`: «its question says so»).
  Fix: «The lead also merges each change of the product's release checklist …». Passes: 2/3

### 10. Описания навыков

Без находок, кроме триггера паузы (F7.1). `epic-files` называет `status` и два PR плана.

### 11. Нейтральность к провайдеру

Без находок. Только `git` и инструменты LADO; `gh` — пример («with `gh`:»).

### 12. Безопасность и рамки

Без находок в изменённом тексте. PR checklist покрыт вопросом `plan_ok` (`epic-files:133`: «covered by the human's yes at `plan_ok` (its / question says so)»). «Never reset the run's branch» остаётся. `git switch -C` снимает падение при второй правке. Мерж в базу без показа коммитов — в разделе «Пропущено ранее», F12.1.

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка без учёта окружения | Закрыта: `epic-files:158` «First tell whether the work caused it»; у конфликта слияния базы своё правило по файлам, «In any other file `git merge --abort` and tell the human» |
| 2. Работа вне flow, мерж без гейта | Закрыта для PR checklist (назван в вопросе `plan_ok`). Открыто вне изменённого текста: F12.1 в «Пропущено ранее» |
| 3. Путь вне worktree рана | Закрыта: `epic-files:153` — абсолютный путь в worktree рана для `write_artifact`; поиск папки через `git diff --name-only origin/<base>...<the run's branch>`. Коммит паузы в чужой момент — F5.1 |
| 4. Вердикт без порога | Закрыта: `epic.yaml:88-89` «Report `ok` when no finding is blocking»; «Blocking: …» в `plan-critic.md`; «**A gap** is» в `delivery.md` |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | Закрыта: у `plan-critic` только справочные навыки |
| 6. Команды не в ладу с `expects.commands` | Закрыта: новые команды — только `git`; `expects commands: git` |
| 7. Read-only роли не в ладу с работой | Закрыта: `read-only roles: plan-critic`, `>=0.34`, README «does not run on Codex CLI» |

## Не прослежено

Ничего: новых элементов нет, жёлтые меры обоснованы, `--compare` совпадает. Где текст чертежа отстаёт от построенного — F5.6.

## Прежние находки

Находки прошлого визита (на 3fa45cc).

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F12.1 [high] PR checklist без «да» | RESOLVED | `epic.yaml:101-102` «It also merges a change of / the product's release checklist by its own pull request at once»; `epic-files:133` «covered by the human's yes at `plan_ok`» (формулировка — F9.1) |
| F3.1 [high] база не возвращается в ветку рана | RESOLVED | `epic-files:142-150` «Bring the base into the run's branch after the plan pull request …, before `readiness` (in `review`, on `finish`) … and in `close` before the closing pull request», с правилом конфликтов; `epic.yaml:157-158`, `:238-239` (остаток для путей в обход `review` — F3.1, low) |
| F2.1 одобрения вне шага | RESOLVED | `epic-files:81-84` «go on the line `Epic: …`», «At intake every answer is a row» (в `run` шаг 0 не синхронизировано — F5.5) |
| F2.2 папка в `intake` | RESOLVED | `supervisor.md:30-31` «or in `intake` the folder the branch adds» (чтение `plan.md` там — F2.2, low) |
| F3.2 `status` и гейты рана | RESOLVED | `epic-files:106-109` «and at every gate or question of a step's run», «with a link to it» |
| F3.3 ручная проверка как вопрос | RESOLVED | `step-review:65` «a new question for the human, a recommended manual check among them» |
| F3.4 строка «шаг закончился» | RESOLVED | `supervisor.md:87-88` «after the review of an ended step: … or "readiness check next"» |
| F3.5 локальная ветка `-checklist` | RESOLVED | `epic-files:134` «`git switch -C <the run's branch>-checklist`» |
| F3.6 вердикты в `brief.md` | RESOLVED | `epic.yaml:62-63` «Rewrite each "missing" verdict of `brief.md` as "covered by S<n>, Check: <line>"»; `epic-files:34-35` |
| F5.1 «push nothing yet» | RESOLVED для `finish` и ветки эпика | `epic.yaml:156-158`; остаток для `next` — F5.4 |
| F5.2 `continue` предела LADO | RESOLVED | `epic.yaml:114-115` «After LADO's `continue` at this step's visit limit, act on the note before / that limit» |
| F5.3 чертёж устарел | RESOLVED | `BLUEPRINT.md:173-176` «The run's branch is never reset»; `:7` и R15 — «the decisions that matter»; `epic.run` — «keeps `status`» (остаток — F5.6) |
| F6.1 два правила разбиения | RESOLVED | `step-review:39` |
| F7.1 где хранится пауза | RESOLVED в навыке | `step-review:71-73`; правило не доходит до лида — F7.1 этого визита |
| F9.1 «a status» | RESOLVED | `epic-files:149-150` «a step's status in / `plan.md`» |

Находки и изменения плана из отчёта на 3fa45cc, отмеченные там RESOLVED, остаются RESOLVED: правки этого визита их тексты не откатывают.

## Вырезанные правила

Правки 8a48e03 и e5f06cb.

| Вырезанное правило (файл:строка в 3fa45cc) | Где оно сейчас |
|---|---|
| `supervisor.md:65-66`, пример «"my point from the design gate"» | правило то же, остались два примера (`supervisor.md:66-67`) |
| `supervisor.md:93-94` «and the human still asked where it stood» | причина остаётся: «(89 messages in one epic)», `supervisor.md:93`; R19 чертежа |
| `epic.yaml:151` «push nothing yet (`run` does)» | `epic.yaml:156-158`: что и когда пушит `review` (неполно для `next` — F5.4) |
| `epic.yaml:62` «Turn each "missing" topic … into its step or open question» | `epic.yaml:62-63` «Rewrite each "missing" verdict … as "covered by S<n>, Check: <line>" or / an open question» |
| `epic-files:137-139` «(a fast-forward). Never reset the run's branch» | `epic-files:142-150`, с возвращённым правилом конфликтов |
| `step-review:39` старое правило «Split» | `epic-files` «plan.md», признаки (`step-review:39` ссылается) |

Утерянных правил нет. Правило конфликтов, утерянное на 5e0efda, возвращено (`epic-files:145-148`).

## Пропущено ранее

Находки в тексте, который diff не трогает. Вердикт они не блокируют и в правиле остановки не считаются.

- **F12.1** [high] `agents/supervisor.md:48`
  > language. Start it from the base branch. Several epics may go at once, one run each.
  LADO создаёт worktree рана от текущего HEAD checkout человека (`lado` `src/lado/runs.py:90`: `runtime.git(sess.repo, "worktree", "add", "-b", run.branch, run.worktree, "HEAD")`; описание `flow_start`: «from your current HEAD»). Переключать этот checkout лиду нельзя (`supervisor.md:52`), а ни один шаг не ставит ветку рана на `origin/<base>`. Если checkout на другой ветке или с невыпушенными коммитами, `run` протащит их в PR плана и сольёт в базу после зелёного CI, хотя ни один гейт их не показывал. Строка не изменилась с 0.1.0 (`git show 7e79334:agents/supervisor.md`, строка 42).
  Fix: в `intake` 1 после `git fetch origin` — `git reset --keep origin/<base>` в worktree рана до первого коммита; это единственное исключение из «Never reset the run's branch». Passes: full pass, confirmed

- **F3.3** [medium] `skills/step-review/SKILL.md:112` (state `close`)
  >   readiness/<key>:<epic branch>`, then `git branch -D readiness/<key>`; when it fails,
  Удаление стоит без условия «только после удачного пуша», а для повторного входа в `close` после потери контекста правила нет (для PR оно есть: «or reuse the one an earlier `close` opened»). Если ветка уже удалена, пуш падает: это уходит человеку как сбой или читается как `stale` и тратит визит `readiness`.
  Fix: «`git push … && git branch -D …`; when `readiness/<key>` is gone and `origin/<epic branch>` holds its commit, skip the push». Passes: full pass, confirmed

- **F3.4** [low] `flows/epic.yaml:163` (state `review`, затем `run`)
  >       resumes S<n> with its tracker task <KEY>", then S<n>'s task draft and brief in full,
  Отменённый ран `<key>` остаётся в сессии, и `flow_start` с именем, которое предписывает `lead-sdlc`, отказывает (`runs.py:1069`: «a run "{wanted}" already exists in session»). Каждый лид выберет другое имя.
  Fix: в `step-brief`, «Starting the step's run»: «a resumed step's run is named `<key>-2`, …». Passes: full pass, confirmed

- **F8.1** [low] `flows/epic.yaml:168` (state `review`, повторный визит после отказа на `step_ok`)
  >       Done when every source is read or marked unavailable with the reason, and the files
  После отказа условие готовности заставляет заново читать все источники того же шага (логи воркеров, ledger, трекер), хотя нужна только правка.
  Fix: «after a rejection at `step_ok`, the sources of the last visit stand». Passes: full pass, confirmed

## Оставлено планом

Нет («Leave: none»).

## Вопросы для человека

1. F12.1 из «Пропущено ранее» (high, с 0.1.0): ран эпика начинается от текущего HEAD вашего checkout. Если он не на базе или с невыпушенными коммитами, они попадут в PR плана и сольются в базу. Исправить до релиза 0.2.0 (одна строка в `intake`) или вынести в 0.2.1? Рекомендация: до релиза: правка маленькая, а мерж в базу трудно откатить.
2. Шесть medium в изменённом тексте (F7.1, F5.1, F5.2, F5.3, F5.4, F2.1), из них четыре — про паузу и пуш. Релизить с ними или сделать ещё один короткий круг? Рекомендация: ещё один круг только для паузы (F7.1, F5.1, F5.2) и F5.4: это одно правило в двух-трёх местах. Остальное — в BACKLOG на 0.2.1.

## Найдено по пути

- `[lado]` `flow_start` не принимает точку старта: ран всегда начинается от текущего HEAD репозитория лида (`runs.py:90`). При этом киты пишут «Start it from the base branch» (`epic`, и `lead-sdlc`: «Start the run from the base branch, so its branch starts there») и не могут выполнить это, не переключая checkout человека. Параметр `base`/`from` у `flow_start` закрыл бы это для всех китов (F12.1).
