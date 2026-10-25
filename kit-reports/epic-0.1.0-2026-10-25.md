# Отчёт о ките: epic 0.1.0

- Дата: 2026-10-25
- Кит: корень worktree прогона `improve/epic-2` (`.lado/worktrees/kit-epic/improve-epic-2`), путь дан шагом
- Коммит: 7e79334
- Оценил: критик kit-builder (слои a и b)
- Режим: полная оценка (шаг `assess` прогона `improve/epic-2`, задача LAD-145). Отчёт `kit-reports/epic-0.1.0-2026-10-19.md` — повторная оценка, не полный отчёт, а текст кита с тех пор менялся (коммиты 97365c0 и др.), поэтому он только фон.
- Проходы: три полных прохода по всему тексту кита и зависимым навыкам (`sdlc` 0.6.0: `sdlc-settings`, `lead-sdlc` = `agents/supervisor.md`, `analyst.md`, `integrator.md`, `task.yaml`; `tracker` из `tracker-yougile` 0.5.0). Проход 1 сделал сам критик (с `kit.yaml` и ролей). Проходы 2 (с flow, по состояниям) и 3 (с навыков, потом роли) сделали свежие независимые субагенты, которым дали только рубрику, `lado-kit-format`, папку кита и папки зависимостей. Находки одного прохода: отброшено 8, оставлено как подтверждённые 19.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3. Обе меры обоснованы в BLUEPRINT.md, раздел 4 |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 24 находки (0 high, 15 medium, 9 low); без находок 2 критерия из 12 (10, 11) |
| Охват | полная оценка всего кита: 9 файлов текста (`kit.yaml`, README, BLUEPRINT, 3 роли, 1 flow, 3 навыка) |

Счёт относится только к тому, что названо в строке «Охват». Его нельзя сравнивать со счётом повторной оценки.

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
# Complexity budget: epic 0.1.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 2 | 3 / 5 | green |
| Work steps in a flow | flows/epic.yaml | 7 | 5 / 8 | yellow |
| Gates in a flow | flows/epic.yaml | 3 | 2 / 3 | yellow |
| Words in a role prompt | agents/delivery.md | 570 | 800 / 1500 | green |
| Words in a role prompt | agents/plan-critic.md | 344 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 819 | 1000 / 1500 | green |
| Own skills | kit | 3 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Flows

![epic](epic-0.1.0-2026-10-25/epic.svg)

```
same as the plan in BLUEPRINT.md (exit status 0)
```

## Исправить в первую очередь

1. Записывать ключ задачи трекера в `plan.md` и коммитить сразу после создания задачи, до `flow_start`; при возобновлении сверять открытые раны `task` по имени ключа (F12.1).
2. Правило «Progress» лида: не каждое изменение состояния рана, а события и измеримый интервал; это же — запрос человека о периодическом информировании (F9.1).
3. Критик плана: признаки слишком большого шага как блокирующая находка и определение S/M/L (F3.1); темы чек-листа «covered» привязывать к шагу и строке его Check (F4.1).
4. Убрать противоречие с `lead-sdlc` о коммите `.lado/sdlc.md` в checkout человека (F5.1) и о языке файлов эпика и артефактов для человека (F5.2).
5. Предел визитов `readiness` после отказа на `delivery_ok` и выбор `continue` / `cancel` в `close` (F8.1, F7.1).

## Находки

### 1. Границы ролей

- **F1.1** [low] `agents/supervisor.md:16`
  > (`epic-files`, "Changing the files"), nothing else of the product.
  Права лида заканчиваются на файлах эпика и release checklist, но `step-review` велит ему сливать базу в ветку эпика и пушить её (`skills/step-review/SKILL.md:79`: «origin/<epic branch>`, `git merge origin/<base>`, `git push origin HEAD:<epic branch>`,»), а в `close` — пушить разрешения конфликтов `delivery`. Лид, читающий свои права буквально, может отказаться от слияния.
  Fix: добавить к правам лида «и на ветке эпика — слияния базы по `step-review`». Passes: 1/3, confirmed

### 2. Передача между шагами

- **F2.1** [medium] `skills/step-review/SKILL.md:25` (state `review`)
  > | the run's talk with the human | `lado log <session> --agent <worker>` for each worker of the run (`list_agents`; without `lado` on PATH, mark it unavailable), and your own messages about the run | the analyst's questions, rejections at gates, trouble in the manual check, leftovers named at the end |
  Имена воркеров берутся из `list_agents`, но на `done` `lead-sdlc` уже завершил их (`sdlc` `agents/supervisor.md:61-62`: «end each worker of the run with `finish_worker`»), до `review`. `list_agents` показывает только живых агентов (в этой сессии нет воркеров прошлого прогона `improve/epic`), так что самый ценный источник по тексту навыка выпадает почти после каждого шага.
  Fix: брать имена воркеров из `flow_status` / нот рана, или читать лог до того, как лид завершит воркеров. Passes: 1/3, confirmed

- **F2.2** [medium] `agents/supervisor.md:27-28`
  > 2. For each open `epic` run, find its folder in that run's worktree (the path
  >    `flow_status` gives): the folder whose `plan.md` State names this run (`Epic run:`),
  После рестарта лид читает `plan.md` из того, что сейчас выписано в worktree. В `readiness` `delivery` держит его на `readiness/<key>` (`skills/step-review/SKILL.md:83`) или на detached `origin/<base>` (`agents/delivery.md:25`), а последние коммиты `review` (переход в `finish` идёт без гейта и без PR плана) есть только в ветке рана. Лид расскажет человеку устаревшее состояние; сам кит это знает (`agents/supervisor.md:87`: «in `readiness` `delivery` has it on another»).
  Fix: читать файлы из ветки рана (`git show <run branch>:docs/epics/<key>/plan.md`), а не из рабочего дерева. Passes: 1/3, confirmed

- **F2.3** [medium] `flows/epic.yaml:38` (state `intake`)
  > Write `docs/epics/<key>/brief.md` and start `decisions.md` (when continuing, add to
  Файлы пишутся только после всех раундов вопросов, а ключ (имя папки) выбирается последним (шаг 4). При потере контекста посреди intake все ответы человека теряются, хотя `epic-files:15` говорит: «what is not in them is lost with» the lead's context; лид спросит человека заново.
  Fix: сначала ключ, затем после каждого раунда дописывать решения в `decisions.md` и коммитить. Passes: 1/3, confirmed

- **F2.4** [low] `skills/step-review/SKILL.md:22` (state `review`)
  > | openspec proposal, "Out of scope" | the archived change on the base (`openspec/changes/archive/`), or the run's `design` | deferred leftovers, the human's decisions with dates |
  На ветке эпика архив шага попадает в ветку эпика, а не в базу: лид ищет не там.
  Fix: «on the step's target branch». Passes: 1/3, confirmed

- **F2.5** [low] `skills/step-review/SKILL.md:24` (state `review`)
  > | integrator's comment | the step's tracker task, with `tracker` | what to check by hand, regression risk, what is left by hand |
  В `sdlc` 0.6.0 интегратор пишет комментарий, только если его просит раздел **After the merge** (`sdlc-settings:20`: «**After the merge** is optional: without it, or without its part for a merge, the»); иначе «что осталось руками» есть только в ноте `merge`, которую ни один источник не называет.
  Fix: добавить «or the integrator's note at `merge`». Passes: 1/3, confirmed

- **F2.6** [low] `skills/epic-files/SKILL.md:40`
  > Phase: <intake|plan|steps|readiness|closed> · Epic run: <run> · Branch: <base | epic/...>
  `step-brief:74` велит «pause the epic in the plan's State», а `epic.yaml:63` — «quote it at the top of `plan`'s State», но в шаблоне State нет поля паузы; и `run`, шаг 3, при возобновлении не снимает паузу (бывшая F8.5). Каждый ран пишет паузу по-своему, лид после рестарта может её не увидеть или увидеть устаревшую.
  Fix: добавить в Phase `paused (<why>)` и в `run`, шаг 3, «remove the pause from State». Passes: 1/3, confirmed (проход 1 — снятие паузы, проход 3 — шаблон)

### 3. Готовность и исходы

- **F3.1** [medium] `agents/plan-critic.md:46`
  > Blocking: the plan cannot be approved as it is (a step without a check, a contradiction,
  Размер шага не входит в блокирующие («a missing topic that changes the plan»), а проверка размера (`plan-critic.md:28`: «A step too large to design in one round of questions») неизмерима; S/M/L из `epic-files:46` («### S<n> — <title> (<S|M|L>)») нигде не определены. Критик пропускает большой шаг с `ok`: в прогоне LAD-116 три шага L прошли `plan_review` и разбивались уже на дизайне (ретро, 3.2: «7 шагов стали 14»).
  Fix: проверяемые признаки шага, который надо разбить (больше одной видимой пользователю возможности; схема и UI вместе; части, мержащиеся по отдельности), как блокирующая находка; S/M/L определить или убрать. Passes: 1/3, confirmed

- **F3.2** [medium] `flows/epic.yaml:215` (state `close`)
  > or the pull request from the epic branch into the base is red from the work: the
  `stale` покрывает отказ пуша и красный CI, но не конфликт закрывающего PR (ветка эпика → база), когда база ушла вперёд за время `delivery_ok`. Такой случай уходит в «Any other failed push … refused merge» → человеку, хотя лид не может разрешить конфликт сам, а результат проверки устарел (бывшая F3.5).
  Fix: добавить «or it conflicts with the base» в условие `stale`. Passes: 1/3, confirmed

### 4. Независимая проверка

- **F4.1** [medium] `agents/plan-critic.md:21`
  > - **Readiness.** A topic of the checklist of `epic-files` the brief leaves missing and no
  Критик ищет только «missing» темы; «covered: where» (`epic-files:29`) может значить «уже есть в продукте» и не требует шага и строки Check. Темы «data and migrations», «update and rollback», «documentation» проверяет только `delivery` в самом конце: в LAD-116 откат миграции впервые проверили на readiness — G1, S8, S9 (ретро, 3.2).
  Fix: в brief «covered by S<n>, Check: …» или «not relevant: …»; критик проверяет, что у шага с миграцией в Check есть путь назад, а у шага с видимым поведением — документы. Passes: 1/3, confirmed

- **F4.2** [medium] `skills/step-review/SKILL.md:79` (state `review`)
  > origin/<epic branch>`, `git merge origin/<base>`, `git push origin HEAD:<epic branch>`,
  Лид сливает базу в общую ветку эпика и пушит без единой проверки, тогда как интегратор `sdlc` после такого слияния прогоняет **Checks** (`integrator.md:31`: «merges cleanly and brought changes, run the settings' **Checks**»). Чисто слитая, но сломанная ветка достаётся аналитику следующего шага, и его разработчик получает красные проверки не от своей работы.
  Fix: прогнать **Checks** после слияния и до пуша; красная проверка от слияния — человеку, как конфликт. Passes: 1/3, confirmed

### 5. Противоречия

- **F5.1** [medium] `agents/supervisor.md:47-48`
  > files"). Your own repository is the human's checkout: read it, never edit, commit in or
  > switch its branch there, but for the one exception `epic-files` names ("Safety on the
  `lead-sdlc` велит тому же встроенному лиду при отсутствии `.lado/sdlc.md` составить его с человеком и закоммитить в своём репозитории (`sdlc` `agents/supervisor.md:22`: «Write and commit it only with their yes: it is»). На новом проекте лид нарушит одно из двух правил.
  Fix: назвать коммит файла настроек вторым исключением, или сказать, что он пишется в worktree рана эпика и идёт PR плана. Passes: 2/3

- **F5.2** [medium] `skills/epic-files/SKILL.md:16`
  > the lead's context. Write them in the language of the openspec documents of `.lado/sdlc.md`
  Артефакты гейтов `epic-brief`, `plan`, `delivery-report` пишутся из этих файлов, то есть на языке openspec, а `sdlc-settings:43` требует для человека «questions, notes, artifacts meant for them, and short progress lines too» на языке чата. Когда языки разные, одни агенты переведут артефакт, другие нет.
  Fix: сказать явно, какой язык у файлов и у артефактов (например, артефакт — файл как есть, нота пересказывает на языке чата). Passes: 1/3, confirmed

### 6. Дублирование

- **F6.1** [low] `skills/step-review/SKILL.md:91`
  > refused because the epic branch moved, report `stale`: the readiness check is out of
  Условие `stale` записано в трёх местах: здесь, в `close` (`epic.yaml:214-216`) и в `epic-files` «When a command fails»; копии уже расходятся — здесь нет «red from the work» (бывшая F6.5).
  Fix: оставить условие только в `close`, здесь — ссылка. Passes: 2/3

- **F6.2** [low] `agents/supervisor.md:81`
  > go to "Later" on their word and you report `finish`, so `delivery` checks readiness.
  То же правило в `review` (`epic.yaml:149`: «when the human asks for the readiness check with steps left, which then go to»).
  Fix: в роли оставить «offer the readiness check (`review`)». Passes: 1/3, confirmed

### 7. Когда звать человека

- **F7.1** [medium] `flows/epic.yaml:218-219` (state `close`)
  > `stale` anyway: LADO holds the run at its limit; show the human the command and its
  > output and recommend `continue` or `cancel`.
  Ничто не говорит, когда рекомендовать `continue`, а когда `cancel`: лид выберет наугад, а `cancel` теряет коммиты, которые есть только в ветке рана (бывшие F7.3, F7.4).
  Fix: условие выбора, например «`continue` when the cause can be fixed by a fresh readiness check, `cancel` otherwise, saying which commits only the run's branch holds». Passes: 1/3, confirmed

### 8. Петли на повторном визите

- **F8.1** [medium] `flows/epic.yaml:176` (state `readiness`)
  > On the third visit report `ready` whatever is open, each gap under "Questions for the
  Третий визит всегда ведёт в `delivery_ok`; отказ там ведёт в четвёртый визит, и LADO держит ран на пределе. Для `plan_ok` (`epic.yaml:63-65`) и `close` (`epic.yaml:217-219`) такой случай описан, для этого — нет: лид импровизирует или молчит.
  Fix: в `delivery_ok` / роли лида — «a rejection after the third readiness visit holds the run: tell the human and recommend `continue` or `cancel`». Passes: 2/3 (уровни medium и low, взят medium)

### 9. Краткость и «почему»

- **F9.1** [medium] `agents/supervisor.md:65-66`
  > - **Progress.** When a step's run moves to its next state, or has waited in one state for
  >   long, tell the human in one line where it is; do not wait to be asked.
  Сообщение на каждый переход рана `task` плюс неизмеримое «for long». В LAD-116 — 89 сообщений лида, и человек трижды спрашивал «где мы?» (ретро, 1 и 3.1): поток вех не даёт обзора, а интервал у каждого лида свой.
  Fix: сообщать по событиям (шаг смержен, эпик ждёт человека не через гейт) и раз в заданный интервал на долгом шаге, со ссылкой на сводку состояния. Passes: 1/3, confirmed

- **F9.2** [low] `skills/step-brief/SKILL.md:36` (и `README.md:62`)
  > remote." The analyst of `sdlc` 0.5.0 targets "the epic's branch when the tracker has
  Установлен `sdlc` 0.6.0, кит версию `sdlc` не закрепляет; строка аналитика в 0.6.0 та же, но номер версии устарел.
  Fix: «The analyst of `sdlc` targets …» без версии. Passes: 3/3

- **F9.3** [low] `skills/step-brief/SKILL.md:58-59`
  > do not overlap. Say in each brief that the other runs alongside, and that the second to
  > merge brings the latest base in first.
  Интегратор и так всегда сливает `origin/<target>` перед мержем (`integrator.md:29-30`: «merge `origin/<target>` into the run's branch»): вторая половина фразы — no-op.
  Fix: убрать «and that the second to merge brings the latest base in first». Passes: 1/3, confirmed

### 10. Описания навыков

Без находок. У каждого навыка есть «Use when», `step-brief` и `step-review` ссылаются друг на друга («for reviewing a step that ended use `step-review`»); каждый навык из `skills:` роли ею используется; `tracker` и `sdlc-settings` в `expects.skills`, их киты названы в README (строки 38-41), `dependencies.lado: ">=0.34"`.

### 11. Нейтральность к провайдеру

Без находок. Нет имён инструментов, моделей и конфигов одного CLI; `gh` — только пример инструмента из настроек («with `gh`:»), `lado log` — команда LADO с запасным путём; путей к файлам кита нет.

### 12. Безопасность и рамки

- **F12.1** [medium] `flows/epic.yaml:115` (state `run`)
  > Mark the step in `plan.md` "in progress" with the key and the run, and commit.
  Ключ попадает в `plan.md` только после создания задачи, проверки записи и старта рана. Если лид теряет контекст в этом окне, после рестарта шаг без ключа, и правило «only for a step without a key, create its tracker task» создаёт вторую задачу в трекере (запись вне машины без «да» на неё) и второй ран; первый остаётся сиротой: «A run of `task` that no plan names is not the epic's» (`agents/supervisor.md:34-35`).
  Fix: записать ключ в `plan.md` и закоммитить сразу после создания задачи, до `flow_start`; при возобновлении сверять открытые раны `task` по имени ключа. Passes: 2/3

- **F12.2** [medium] `flows/epic.yaml:203` (state `close`)
  > branch into the base (or reuse the one an earlier `close` opened), wait for green CI and merge it. This comes before step 3,
  Способ мержа нигде не задан — ни для ветки эпика, ни для PR плана (`epic-files:92`: «and merges it, with the code host and its tool»), тогда как интегратор `sdlc` требует «with a merge commit, never squash or rebase» (`integrator.md:37`). Squash ветки эпика схлопнет мерж-коммиты всех шагов в базе; удалять ветку эпика на remote после мержа тоже ничто не велит.
  Fix: в `epic-files` «Changing the files»: «merge with a merge commit, never squash or rebase», после мержа ветки эпика — удалить её на remote. Passes: 1/3, confirmed

- **F12.3** [medium] `skills/step-brief/SKILL.md:28`
  > 1. **Step.** "Step S<n> of epic <key>: <title>." The goal of the epic in one line.
  Бриф передаётся аналитику целиком «as the human's words» (`step-brief:65`), а стоящие правила защищают только `docs/epics/` (`step-brief:47`), но не запрещают нести номера шагов эпика в файлы продукта. В LAD-116 «not built yet (step S6)» попало в `projects.md` и `ui.md`, понадобились решение #1408 и шаг S8 (ретро, 3.2).
  Fix: стоящее правило «epic step numbers and links to `docs/epics/` never go into the product's code, docs, specs or UI»; ревью шага и `delivery` ищут номера шагов плана в диффе. Passes: 1/3, confirmed

- **F12.4** [low] `flows/epic.yaml:19` (state `intake`)
  > 1. Read the statement, the code and docs it touches, and `docs/epics/` on the base
  Лид стартует в checkout человека (`epic-files:104`: «The lead starts in»), который может быть на другой ветке или отставать от remote; «on the base» не говорит, как читать. Существующий эпик можно не заметить и завести дубль папки (known hole 3).
  Fix: «`docs/epics/` of `origin/<base>` after `git fetch`». Passes: 1/3, confirmed

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка без учёта окружения | Закрыта: `epic-files:110` «First tell whether the work caused it», `delivery.md:52` «a red check from outside the work (`epic-files`, "When a command fails") are not gaps». Слияние базы в ветку эпика вообще без проверки — F4.2 |
| 2. Работа вне flow, мерж без гейта | Закрыта: `epic-files:91` «The lead pushes and opens it only after the human's yes at a gate»; аудит плана — «change the plan on their yes» внутри рана; `supervisor.md:72` «Start a run of `task` only for a step the human approved» |
| 3. Путь вне worktree рана | Почти закрыта: `epic-files:84` «Edit and commit only in the epic run's worktree», `epic-files:105` абсолютный путь для `write_artifact`. Открыто: F12.4 (чтение `docs/epics/` на старте), F2.2 (worktree выписан не на ветку рана) |
| 4. Вердикт без порога | Закрыта: `plan-critic.md:46` «Blocking: the plan cannot be approved as it is», `epic.yaml:82-83` «Report `ok` when no finding is blocking», определение «A gap» в `delivery.md:48`. Размер шага вне порога — F3.1 |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | Закрыта: у `plan-critic` нет `tracker`, его навыки для него только читаются; `delivery` идёт к человеку только через лида (`epic-files:116-117`) |
| 6. Команды не в ладу с `expects.commands` | Закрыта: `expects commands: git`; `gh` — из настроек (`epic-files:92`); `lado log` с запасным путём («without `lado` on PATH, mark it unavailable»); `>=0.34` ≥ 0.30 |
| 7. Read-only роли не в ладу с работой | Закрыта: `read-only roles: plan-critic`, «You change no file and commit nothing»; `delivery` и лид пишут и не read-only; `>=0.34`; README:46 «The role `plan-critic` is read-only, so the kit does not run on Codex CLI.» |

## Не прослежено

Ничего. Каждая роль, шаг, гейт и навык названы требованиями R1–R17 (BLUEPRINT, раздел 3, обратная проверка); жёлтые меры (7 шагов, 3 гейта) обоснованы в разделе 4; `--compare` разницы не печатает.

Для `triage`: требования LAD-145 меняют требования чертежа — R1 LAD-145 противоречит обоснованию `epic.step_ok` («creating a tracker task … are theirs»), R3 — R8 чертежа («On long steps the lead gives a short progress report»), R10 — R5 чертежа («Every decision is recorded»). Их нужно переписать в чертеже явно, иначе следующая оценка покажет дрейф.

## Связь с LAD-145 и бэклогом 0.1.1

- F9.1 → R3 и просьба человека о периодическом информировании; F3.1 → R6; F4.1 → R7; F12.3 → R8.
- Открытые находки `BACKLOG.md` «epic 0.1.1», найденные снова: F3.5 → F3.2, F7.3 и F7.4 → F7.1, F8.5 → F2.6, F6.5 → F6.1, F8.4 близка к F8.1. F5.3 нашёл один проход, вред в файлах не подтвердился — отброшена.

## Вопросы для человека

1. R1: отдать лиду создание задачи и старт шага без `step_ok`, пока ревью не меняет план? Рекомендация: да, но с механическим определением «план не изменился», с явным вопросом об этом на `plan_ok` и с одной строкой человеку «S<n> пошёл без гейта» (как в разборе).
2. R10: заменить запись каждого подтверждения счётчиком на шаг (меняет R5 чертежа)? Рекомендация: да, счётчик с ID гейтов сохраняет долю принятых рекомендаций.
3. R11: обязательная ручная проверка лида? Рекомендация: нет в этом виде; источник «была ли ручная проверка видимого поведения» в `step-review` и рекомендация человеку, если не было.
4. Интервал строки о ходе долгого шага (F9.1): рекомендация — около часа, только пока шаг идёт и ничего не ждёт человека, со ссылкой на сводку состояния.
5. Вносить ли в 0.2.0 открытые находки бэклога 0.1.1, совпавшие с этой оценкой (F3.2, F7.1, F2.6, F6.1)? Рекомендация: да, те, что касаются того же текста, что меняет LAD-145.
