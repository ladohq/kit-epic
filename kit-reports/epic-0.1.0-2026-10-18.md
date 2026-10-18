# Отчёт о ките: epic 0.1.0

- Дата: 2026-10-18
- Кит: корень worktree прогона `improve/epic` (`.lado/worktrees/kit-epic/improve-epic`), путь дан шагом
- Коммит: 97365c0
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка, третий визит `evaluate`, относительно предыдущего отчёта (этот же файл, коммит ba7736c, кит на 49eb27c). База — коммит, который называет план: `git diff e9b37a0 -- kit.yaml README.md BLUEPRINT.md agents flows skills`. Последний цикл исправлений — `git diff ba7736c`.
- Проходы: три прохода по изменённому тексту и его окружению. Проход 1 — сам критик, по последнему diff по порядку. Проход 2 — свежий субагент, начало с flow. Проход 3 — свежий субагент, начало с навыков. У субагентов были оба diff, рубрика и зависимые тексты sdlc 0.5.0, но не прежний отчёт. Проходы не дали ни одной общей находки: проход 1 не нашёл ничего, проходы 2 и 3 — по две разные. Все находки одного прохода проверены по файлам и оставлены как подтверждённые (4), отброшенных нет. Полный проход по 7 файлам, которые затронул diff (`kit-rubric`, «Re-evaluation» 5), сделал отдельный субагент. Его находки подтверждены по файлам: 4 — в изменённом тексте (2 совпали с находками проходов), 2 — в «Пропущено раньше». Проверку вырезанных правил критик провёл сам.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3. Обе меры обоснованы в BLUEPRINT.md, раздел 4 |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 7 находок в изменённом тексте (0 high, 4 medium, 3 low); без находок 8 критериев из 12 (1, 2, 4, 5, 9, 10, 11, 12). Из 7 прежних находок 7 RESOLVED; в «Пропущено раньше» 4 (2 новые, 2 прежние) |
| Охват | повторная оценка изменённого текста с полным проходом по 7 файлам, которые затронул diff |
| Правило остановки | не выполнено: 0 high, 4 medium. Это совет для гейта релиза, а не запрет |

Счёт относится только к тому, что названо в строке «Охват». Его нельзя сравнивать со счётом полной оценки.

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

![epic](epic-0.1.0-2026-10-18/epic.svg)

```
same as the plan in BLUEPRINT.md
```
(код выхода 0)

## Исправить в первую очередь

Блокирующих находок нет. Если будет ещё один круг, то начать с этих:

1. `stale` не покрывает PR из ветки эпика в базу, который конфликтует, потому что база ушла вперёд, пока шёл `delivery_ok`. Прогон ждёт человека, а нужен свежий `readiness` (F3.5).
2. На паузе и на лимите `readiness` рекомендуют `cancel`, но коммиты, которые есть только на ветке прогона (ключ шага, строки решений, пауза), при отмене теряются (F7.4).
3. После `continue` за лимитом правило третьего визита `readiness` («report `ready` whatever is open») неясно для четвёртого визита, и красный PR не превращается в шаг-исправление (F8.4).
4. Когда советовать `continue`, а когда `cancel` на лимите в `close`, не сказано (F7.3).

## Находки

### 1. Границы ролей

Новых находок нет. Возобновлённый шаг, повторное использование ключа и слияние PR из ветки эпика делает только ведущий. Права delivery не менялись.

### 2. Передача между шагами

Новых находок нет. Бриф шага на паузе идёт в артефакте: `flows/epic.yaml:140-141` «then S<n>'s task draft and brief in full, / which its resumed run starts with», и `run` читает `step-brief`.

### 3. Когда шаг готов и какой исход

- **F3.5** [medium] `flows/epic.yaml:214` (state `close`)
  >       in step 2 the push of the readiness branch is refused because the epic branch moved,
  `stale` покрывает отказ push и красный CI, но не случай, когда база ушла вперёд, пока ждали `delivery_ok`. Тогда PR из ветки эпика в базу конфликтует или отклоняется («branch is behind»). `epic-files:114` относит это к причинам вне работы («push or merge, branch protection, a flaky CI run…»), и ведущий ждёт человека. А для решения нужен merge базы с разрешением конфликтов, который ведущему делать нельзя и который сделал бы свежий `readiness`. Чертёж прямо называет этот случай причиной для `stale` («cannot be merged as it is», `BLUEPRINT.md:205`).
  Fix: добавить к условию `stale` «or the pull request cannot merge because the base moved (a conflict, or behind a protected base)». Passes: full pass, confirmed.

- **F3.7** [low] `skills/epic-files/SKILL.md:101`
  >   both sides (newest last); in `plan.md` take the newer side and check it against
  «The newer side» не определена. Merge идёт, только когда на ветке прогона есть неслитые коммиты, так что свежий State и статусы обычно на её стороне («ours»). Агент может понять «newer» как только что полученную базу и выбросить именно то, от чего предостерегает строка 99. Сверка с `decisions.md` статусы не вернёт.
  Fix: «in `plan.md` keep the run's branch's side, then add steps the base's side has». Passes: 1/3, confirmed. Строка 99 «would drop commits not on the base yet (rows of `decisions.md`, statuses)» стоит рядом, а статусов в `decisions.md` нет (`epic-files`, «decisions.md»: только решения).

### 4. Независимая проверка

Новых находок нет. После `stale` delivery проверяет заново, и человек одобряет снова на `delivery_ok`, прежде чем повторится какое-либо слияние.

### 5. Противоречия

Новых находок нет. Ask `step_ok` теперь совпадает с `run` шаг 3: `flows/epic.yaml:159-160` «creates the step's tracker task (or reuses the paused / step's)».

### 6. Дублирование

- **F6.5** [low] `skills/step-review/SKILL.md:91`
  >   refused because the epic branch moved, report `stale`: the readiness check is out of
  Условие `stale` записано ещё и в `close` (`flows/epic.yaml:214`). `epic-files` теперь только ссылается на `close`, а step-review по-прежнему повторяет часть условия. Если исправлять F3.5, правок будет две.
  Fix: в step-review оставить git-команды и «report as `close` says». Passes: full pass, confirmed (и в прошлых визитах эту находку дал один проход).

### 7. Когда звать человека

- **F7.3** [medium] `flows/epic.yaml:218-219` (state `close`)
  >       `stale` anyway: LADO holds the run at its limit; show the human the command and its
  >       output and recommend `continue` or `cancel`.
  Что советовать в каком случае, не сказано. У `lead-sdlc` есть условие («recommend `continue` (one more review visit) when the open findings are being fixed, or `cancel`»), здесь его нет. А человек уже принял эпик на `delivery_ok`, и `cancel` оставит открытыми PR в базу, план и задачу в трекере, о чём ему не скажут. Совет будет разным от прогона к прогону и может склонить к потере принятого результата.
  Fix: «recommend `continue` (one more readiness check of the approved result); `cancel` only when the human gives up the epic, and then say what stays open (the pull request into the base, the plan's State, the tracker task)». Passes: 1/3, confirmed. Цитата и условие `lead-sdlc` (`agents/supervisor.md:50-52` кита sdlc) на месте.

- **F7.4** [medium] `flows/epic.yaml:142` (state `review`, пауза; то же в `close`, `:219`)
  >       waits for `sdlc`, or the human cancels the run. When you come back from `readiness` with
  Отмена предлагается, но коммиты, которые есть только на ветке прогона, при ней теряются: ключ шага на паузе (записан после слияния последнего PR плана, `:115`), строки решений о двух промахах и выборе «wait», пауза в State. Правка плана попадает в базу только PR «after the human's yes at a gate» (`epic-files:91`), а удалённую ветку удалили после прошлого PR плана. Следующий прогон `epic`, который продолжит эпик, читает `docs/epics/` на базе (`intake` 1). Он найдёт S<n> «planned» без ключа, `run` создаст вторую задачу, а журнал решений (R5) потеряет строки.
  Fix: на паузе и на лимите советовать перед любым `cancel` слить правку плана (пауза, ключ, решения). Разрешить это одним PR плана по «да» человека в чате и записать исключение в `epic-files`, «Changing the files». Passes: full pass, confirmed.

### 8. Циклы при повторном визите

- **F8.4** [medium] `flows/epic.yaml:176` (state `readiness`, после `stale` и `continue`)
  >       On the third visit report `ready` whatever is open, each gap under "Questions for the
  `close` теперь сообщает `stale` и на лимите, так что после `continue` возможен четвёртый визит `readiness`. Касается ли его правило «third visit», не сказано. Красный PR по вине работы — это пробел (`agents/delivery.md:49` «a red check the work causes»), и починить его можно только через `gaps` → `review`. Если delivery прочтёт правило как «третий визит и дальше», он снова сообщит `ready`. Тогда прогон пойдёт по кругу `delivery_ok` → `close` → `stale` → лимит, ничего не исправив. Каждый круг проходит через гейт, так что круг не бесконечен, но выходом остаётся только `cancel`.
  Fix: «On the third visit, unless the note is `close`'s `stale`, report `ready` whatever is open; after `continue` past the limit, report `gaps` as on any visit». Passes: 1/3, confirmed. Строки `:176` и `:218` и определение пробела на месте.

- **F8.5** [low] `flows/epic.yaml:115` (state `run`, возобновлённый шаг)
  >          Mark the step in `plan.md` "in progress" with the key and the run, and commit.
  При возобновлении никто не убирает паузу из State, которую поставил `skills/step-brief/SKILL.md:74-75` («pause the epic in the plan's State until»). Ведущий после рестарта читает State первым и сочтёт эпик на паузе, хотя его шаг идёт.
  Fix: добавить в шаг 3 «and remove the pause from the State». Passes: 1/3 и полный проход (проход 3 и полный нашли одно и то же).

### 9. Краткость и зачем

Новых находок нет. Пустой коммит State убран, у новых правил есть причины: `:201-202` «so the base never says "closed" for a result that is not on it».

### 10. Описания навыков

Новых находок нет. Описания не менялись.

### 11. Нейтральность к провайдеру

Новых находок нет. `continue`, `cancel` и `flow_status` — слова и инструменты LADO.

### 12. Безопасность и рамки

Новых находок нет. PR из ветки эпика сливается только после `delivery_ok` и зелёного CI; повторное использование PR не добавляет push. Что теряется при `cancel`, описано в F7.4.

## Известные дыры

| Известная дыра | Находка или как кит с ней справляется |
|---|---|
| 1. Красная проверка возвращается без учёта окружения | `epic-files:108` «First tell whether the work caused it»; `stale` только при «red from the work». Конфликт из-за ушедшей вперёд базы ошибочно попадает в причины вне работы — F3.5 |
| 2. Работа вне flow, merge без гейта | merge только после `plan_ok`, `step_ok`, `delivery_ok`; аудит ждёт гейта (`supervisor.md:86-88`) |
| 3. Путь вне worktree прогона | новых путей нет |
| 4. Вердикт без порога важности | без изменений: «Report `ok` when no finding is blocking», «**A gap** is» |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | без изменений |
| 6. Команды не совпадают с `expects.commands` | без изменений: `expects commands: git` |
| 7. Read-only роли не совпадают с тем, что делают | без изменений: `read-only roles: plan-critic`, `lado: ">=0.34"`, README про Codex CLI |

## Не прослежено

Нет. `--compare` печатает «same as the plan in BLUEPRINT.md». Новых элементов нет. Обе жёлтые меры обоснованы в разделе 4, число слов совпадает со скриптом. `BLUEPRINT.md:205` («bounded by its `max_visits`») по-прежнему верно.

## Прежние находки

Находки предыдущего отчёта (ba7736c):

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F2.2 пауза затирает бриф | RESOLVED | `flows/epic.yaml:139-141` «the line "No step proposed…", then S<n>'s task draft and brief in full»; `:111` «started with the brief under the pause line» |
| F7.2 `close` на лимите без выхода | RESOLVED | `flows/epic.yaml:217-218` «report / `stale` anyway: LADO holds the run at its limit». Осталось условие совета — F7.3 |
| F3.1 у «planned» нет ключа | RESOLVED | `epic-files:47` «planned (<KEY>, paused)»; `step-brief:74`; ask `step_ok` «or reuses the paused / step's» |
| F5.2 исключение `stale` без условий | RESOLVED | `epic-files:115-116` «(`close`'s / `stale`, as `close` says» |
| F3.4 правило конфликта для `plan.md` | RESOLVED | `epic-files:100-102`. Неясное «newer side» — F3.7 (low) |
| F8.3 повторное открытие PR | RESOLVED | `flows/epic.yaml:202-203` «(or reuse the one an earlier `close` opened)» |
| F9.2 пустой коммит State | RESOLVED | предложение удалено; `grep -n "back to \`readiness\`" flows/epic.yaml` пусто |
| F2.3 (Пропущено раньше) | STILL OPEN | `flows/epic.yaml:101-104` без изменений |
| F6.4 (Пропущено раньше) | STILL OPEN | `agents/supervisor.md:80` без изменений |

Находки отчёта `assess` и первого визита `evaluate` остаются RESOLVED: последний diff их мест не трогал.

## Вырезанные правила

Все правила, удалённые до ba7736c, проверены в прошлых визитах (таблица там же, в git). Удалено в последнем diff:

| Удалённое правило (файл:строка на ba7736c) | Где оно сейчас |
|---|---|
| `flows/epic.yaml:214-216` «First set the State of `plan.md` back to `readiness` and commit it» | не нужно: шаг 3 (закрытие) теперь после шага 2, и при `stale` State не менялся (F9.2) |
| `flows/epic.yaml:216-218` «do not report `stale`: show the human … stay in `close`» | `:217-219`: `stale` всё равно, LADO держит прогон, показать команду и вывод человеку |
| `skills/epic-files/SKILL.md:115-116` «`close`'s `stale` for the push of the readiness branch and the pull request from the epic branch into the base» | ссылка «as `close` says»; условия в `flows/epic.yaml:213-216` |
| `skills/epic-files/SKILL.md:100` «keep the rows of both sides» для всех файлов эпика | `:100-102`: для `decisions.md`; `plan.md` — более новая сторона; остальное — abort |

Потерянных правил нет.

## Пропущено раньше

Эти находки в тексте, который этот прогон не менял. Вердикт они не блокируют.

- **F5.3** [medium] `skills/step-brief/SKILL.md:72`
  >   design is approved as it is, and later briefs target the base.
  Это «Switch to the base» после второго промаха. Дизайн шага аналитик sdlc писал, поставив ветку прогона на старую базу (`agents/analyst.md:30-31` кита sdlc: `git reset --keep` на `origin/<target>`), а кода прежних шагов там не было: он жил только на ветке эпика. Слияние ветки эпика в базу ветку шага не обновит. Дизайн, одобренный «as it is», описывает код без S1..S(n−1), и разработчик строит на устаревшей ветке. Разработчик sdlc вливает цель, только чтобы разрешить конфликт или красную проверку.
  Fix: после слияния ветки эпика в базу советовать один раз отклонить дизайн: «the epic branch is now on the base: merge `origin/<base>` into the run's branch and revise the design». Passes: full pass, confirmed.

- **F2.4** [low] `flows/epic.yaml:24-25` (state `intake`, продолжение эпика)
  >          old one kept, and `plan.md` keeps its done steps and their numbers, its State
  `Epic run:` в State по-прежнему называет старый прогон, а ведущий после рестарта ищет папку именно по нему (`agents/supervisor.md:28`). Запасной путь — ключ в `epic-brief`, но он там есть, только когда у эпика есть задача в трекере.
  Fix: «its State back to `plan` with `Epic run:` this run». Passes: full pass, confirmed.

- **F2.3** [medium] STILL OPEN `flows/epic.yaml:101-104`: комментарий с гейта применяется к `plan.md`, но не к `step-brief`, с которым стартует шаг.

- **F6.4** [low] STILL OPEN `agents/supervisor.md:80`: правило «проверка готовности по просьбе человека» повторено в роли и в `review`.

## Оставлено планом

Нет: план ничего не оставляет («Leave: none»).

## Вопросы человеку

1. Выпускать 0.1.0, если правило остановки не выполнено (4 medium в изменённом тексте, все на редких путях: пауза, лимит `readiness`, база ушла вперёд во время `delivery_ok`)? Рекомендация: выпускать и завести F3.5, F7.3, F7.4 и F8.4 (и F5.3 из «Пропущено раньше») задачами на 0.1.1. Каждый круг здесь находил новые краевые случаи в путях, которые сам же добавлял, а основной путь эпика (план → шаги → готовность → закрытие) три круга подряд без high.

## Найдено по пути

- Имя файла отчёта совпадает с отчётом `assess` (версия 0.1.0, та же дата), поэтому файл переписывается на каждом визите. Прежние версии: git 256b4c4 (assess), 7619f57 и ba7736c (визиты `evaluate`), артефакт `assessment`.
