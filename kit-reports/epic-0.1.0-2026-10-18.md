# Отчёт о ките: epic 0.1.0

- Дата: 2026-10-18
- Кит: корень worktree прогона `improve/epic` (`.lado/worktrees/kit-epic/improve-epic`), путь дан шагом
- Коммит: 6651bcf
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно отчёта `assess` (тот же файл, коммит 256b4c4, кит на e9b37a0), `git diff e9b37a0 -- kit.yaml README.md BLUEPRINT.md agents flows skills`. База e9b37a0 — коммит, который план называет для своего отчёта.
- Проходы: три прохода по изменённому тексту и его окружению. Проход 1 сделал сам критик (начал с diff по порядку). Проходы 2 (с flow) и 3 (с навыков) сделали свежие независимые субагенты; у них были diff, рубрика и зависимые тексты sdlc 0.5.0, а прежнего отчёта не было. Отдельный субагент сделал полный проход по всем 6 файлам, которые затронул diff (`kit-rubric`, «Re-evaluation» 5). Его запускали заранее, параллельно с проходами. Проверку вырезанных правил критик провёл сам, по `git diff --word-diff` и таблице автора. Находки одного прохода: отброшено 3, оставлено как подтверждённые 0. Находки полного прохода: 1 оставлена как подтверждённая (F3.3), 1 вынесена в «Пропущено раньше», 1 отброшена.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3. Обе меры обоснованы в BLUEPRINT.md, раздел 4 |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 9 находок в изменённом тексте (1 high, 5 medium, 3 low), из них 1 по проверке вырезанных правил; без находок 6 критериев из 12. Из 14 прежних находок 14 RESOLVED; 1 находка в «Пропущено раньше» |
| Охват | повторная оценка изменённого текста с полным проходом по 6 файлам, которые затронул diff |
| Правило остановки | не выполнено: 1 high, 5 medium. Это совет для гейта релиза, а не запрет |

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
| Words in the lead's prompt | agents/supervisor.md | 789 | 1000 / 1500 | green |
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

1. В `close` шаг 1 сливает в базу «closed», отчёт и чеклист релиза раньше шага 2, который может кончиться `stale`. На ветке эпика сначала надо пройти шаг 2. Или при `stale` вернуть State в `readiness` и слить это (F8.1).
2. Пауза эпика: `step_ok` показывает старый `step-brief`, и «да» создаёт вторую задачу в трекере. На паузе надо переписать `step-brief`, а в `run` переиспользовать ключ шага (F3.1).
3. Вернуть «a refused push or merge» в причины вне работы, с `stale` у `close` как единственным исключением (F3.2).
4. Определить «closing pull request» только как PR из ветки эпика в базу. Красный PR плана в `close` чинит ведущий (F5.1).
5. Сказать, что будет на лимите `readiness`: теперь в него ведут три пути (F8.2).

## Находки

### 1. Границы ролей

Новых находок нет. delivery по-прежнему «push nothing», проверка на отсоединённом `origin/<base>` этого не меняет. Ведущий владеет PR плана и всеми push.

### 2. Передача между шагами

Новых находок нет. Ветка эпика после `stale` получает в note команду и вывод: `flows/epic.yaml:208` «and `delivery` checks again; note_body the command and its output». `readiness` их читает (`:170`). Что этого не видит человек, описано в F7.1.

### 3. Когда шаг готов и какой исход

- **F3.1** [medium] `flows/epic.yaml:137` (states `review` → `step_ok` → `run`)
  >       the step back to "planned"), write `retro` and the State with the pause and report
  На паузе `step-brief` не переписывают, и `step_ok` показывает черновик только что отменённого шага. У этого шага уже есть задача в трекере. Если человек одобрит, `run` выполнит `:110` «3. For each approved step, create its tracker task from `step-brief` with `tracker`» и создаст вторую задачу, причём по старому брифу. Строка `:143` «Report `next` with the revision and the next step's draft» тоже спорит с «no new step». Как возобновить эпик, человеку не сказано.
  Fix: на паузе переписывать `step-brief` целиком: «no step proposed: the epic is paused (why); approving resumes S<n> with its tracker task <KEY>». В `run` шаг 3 брать ключ шага из `plan.md`, если он есть, и создавать задачу только для шага без ключа. Passes: 3/3.

- **F3.2** [medium] `skills/epic-files/SKILL.md:114`
  > branch the kit does not own or a refused merge, branch protection, a flaky CI run, a host
  Раньше любой отклонённый push считался причиной вне работы и шёл к человеку. Теперь так считается только push в ветку, «the kit does not own», а `stale` покрывает лишь push ветки readiness в `close`. Отказы push в ветки самого кита больше не подходят ни под одно правило: ветка прогона в `run` шаг 1 (например, защита или права) и push в ветку эпика после каждого шага (`skills/step-review/SKILL.md:79`), который может отклонить слияние шага, идущее одновременно. У ведущего нет ни исхода, ни пути к человеку.
  Fix: «a refused push or merge (but `close`'s `stale`)». Passes: 3/3 (влияние medium по проходам 2 и 3, проход 1 дал low). Это же правило потеряно при сокращении, см. «Вырезанные правила».

- **F3.3** [low] `skills/epic-files/SKILL.md:98`
  >   `git reset --keep origin/<base>`; otherwise `git merge origin/<base>`. A reset at any
  После squash- или rebase-слияния всегда идёт ветка с merge. Если между push и слиянием (например, пока ждали CI) в ветку закоммитили строку `decisions.md`, `git merge origin/<base>` конфликтует на `decisions.md`. Что делать с конфликтом, не сказано.
  Fix: «on a conflict in the epic's files, keep the rows of both sides (newest last) and commit; in any other file `git merge --abort` and tell the human». Passes: full pass, confirmed. Субагент воспроизвёл это в scratchpad: squash коммита A, затем коммит C в ветке, затем merge main дают `CONFLICT (content): Merge conflict in d.md`.

### 4. Независимая проверка

Новых находок нет. `stale` проводит работу заново через `readiness` (delivery) и гейт `delivery_ok`, и только потом merge повторяется: `flows/epic.yaml:207-208` «the readiness check is out of date / and `delivery` checks again».

### 5. Противоречия

- **F5.1** [medium] `skills/epic-files/SKILL.md:112` против `:110` (state `close`, `flows/epic.yaml:207`)
  > epic branch moved, or a red CI of the closing pull request from the work, is `close`'s
  > regression) is the work's: the lead fixes a plan change in the epic run's worktree; at
  «Closing pull request» определён только в ask `delivery_ok` (`flows/epic.yaml:182` «The lead merges the closing pull request; on an epic»). При стратегии с базой закрывающий PR — это PR плана из `close` шаг 1. Красный CI на нём по вине изменения (например, битая ссылка в `report.md`) подходит под оба правила: «the lead fixes a plan change» и `stale`. Один ведущий починит на месте, другой потратит визит `readiness` из трёх.
  Fix: в `close` и `epic-files` писать «the pull request from the epic branch into the base (`close` step 2)». Красный PR плана в `close` чинит ведущий, как говорит `epic-files`. Passes: 2/3 (проходы 2 и 3; полный проход нашёл то же).

### 6. Дублирование

Новых находок нет. Правило reset/merge живёт в одном месте (`epic-files`). Правило о двух шагах теперь ссылка: `flows/epic.yaml:135` «(two only as `step-brief` says, "Two steps at once")». Исключение для checkout ведущего — тоже ссылка (`agents/supervisor.md:48-49`).

### 7. Когда звать человека

- **F7.1** [low] `skills/step-review/SKILL.md:91` (потерянное правило; строка на e9b37a0)
  > -  refused because the epic branch moved, tell the human; the readiness check is out of
  Человек одобрил поставку на `delivery_ok`, а после `stale` прогон молча уходит обратно в `readiness`. В `readiness` после `stale` (`flows/epic.yaml:170`) сказано только «check again from the start», без указания вынести отказ в отчёт. На втором `delivery_ok` человек видит отчёт, где не сказано, почему поставка вернулась. Влияние low: ведущий сообщает о гейте сам (`lead-sdlc`, «A gate opens»).
  Fix: в `readiness` после `stale` процитировать команду и вывод из note в начале отчёта, как это уже делается для отказа на `delivery_ok`. Passes: cut-rule check.

### 8. Циклы при повторном визите

- **F8.1** [high] `flows/epic.yaml:193-195` (state `close`)
  >       1. Record their answer in `decisions.md`, mark `plan.md` closed (State: "closed", the
  >          date, a link to `report.md`) and commit with the report in the run's worktree;
  >          merge it as a plan change.
  Шаг 1 сливает в базу «closed» `plan.md`, `report.md` и проверки, которые delivery перенёс в чеклист релиза («in the same commit as the report», `agents/delivery.md:46`). Шаг 2 идёт позже и может кончиться `stale` (`:206` «the push of the readiness branch is refused because the epic branch moved, or the»). Тогда прогон возвращается в `readiness`, а база говорит, что эпик закрыт, и держит проверки релиза для кода, которого в базе нет. Ведущий после рестарта читает State первым (`agents/supervisor.md`, «At every start and resume») и сообщает человеку, что эпик закрыт. Новый `intake` видит закрытый эпик. Ни `close`, ни `readiness` не велят вернуть State, а второй визит `close` пишет «closed» повторно. Это изменение в базе, которое не убрать без нового PR.
  Fix: на ветке эпика сначала шаг 2 (push ветки readiness, зелёный PR из ветки эпика в базу), потом слить план с шагом 1. Перед `stale` вернуть State в `readiness` в worktree прогона и закоммитить: в базу это уйдёт следующей правкой плана. Passes: 3/3 и полный проход (влияние high по проходу 3, проходы 1, 2 и полный дали medium; взято высшее).

- **F8.2** [medium] `flows/epic.yaml:160` (state `readiness`), с `:170`
  >     max_visits: 3
  >       `close` the note gives the refused push or the red CI: check again from the start.
  `stale` — третий путь обратно в `readiness`, рядом с `gaps` и отказом на `delivery_ok`, а лимит прежний. Один круг `gaps` и один `stale` уже дают третий визит. В `do` нет ни слова о лимите, в отличие от `plan_review` (`:82-84`). Чертёж на него опирается (`BLUEPRINT.md:205` «bounded by its `max_visits`»), но кто и что говорит человеку на лимите, не сказано. Правило `lead-sdlc` о лимите говорит только о ledger.
  Fix: в `readiness` сказать, что делать на третьем визите (сообщить о готовности, открытые пробелы — в вопросах человеку). В `close`, если `stale` превысил бы лимит, принести отказ человеку вместо `stale`. Passes: 2/3 (проходы 2 и 3; полный проход дал low).

### 9. Краткость и зачем

- **F9.1** [low] `skills/epic-files/SKILL.md:96`
  >   did not, so the next push of the run's branch is not refused after a squash merge.
  При новом условии ветку прогона либо сливают с базой, либо перематывают на неё вперёд, так что следующий push всегда fast-forward к её копии на remote. Причина удаления удалённой ветки не верна, и шаг ничего не меняет. Он безвреден, но читатель потратит на него действие и поверит неверному «зачем».
  Fix: убрать удаление и его причину. Или назвать настоящую причину, если она есть (например, чистота хоста). Passes: 2/3 (проход 3 — находка; проход 2 отметил то же в проверке исправлений: «never actually needed, but it does no harm»).

### 10. Описания навыков

Новых находок нет. Описания не менялись. Хост кода берётся из `sdlc-settings` **Git**, где сказано «the code host and its command-line tool».

### 11. Нейтральность к провайдеру

Новых находок нет. `skills/epic-files/SKILL.md:92` «with the code host and its tool from `sdlc-settings` (**Git**)»; `gh` остался только примером.

### 12. Безопасность и рамки

- **F12.1** [medium] `agents/supervisor.md:85`, с `agents/delivery.md:25`
  >   the plan on their yes: commit it in the epic run's worktree and write `plan` again; it
  >    **On the base**: `git fetch origin`, `git switch --detach origin/<base>`, check there,
  Ведущий и delivery работают в одном worktree прогона эпика. Аудит можно попросить в любом состоянии. Во время `readiness` delivery держит HEAD отсоединённым на `origin/<base>` (новое) или на `readiness/<key>` (`skills/step-review/SKILL.md:83`). Коммит ведущего тогда попадает на отсоединённый HEAD и теряется при `git switch <the run's branch>`. Или попадает в ветку readiness, а значит в ветку эпика при `close`, без проверки. Незакоммиченная правка может помешать переключению.
  Fix: коммитить правку аудита только пока прогон в состоянии ведущего (`run`, `review`, гейт); иначе держать её до следующего такого состояния. Либо delivery проверяет в своём `git worktree add --detach <temp> origin/<base>` и удаляет его по имени. Passes: 2/3 и полный проход (влияние medium по проходу 3 и полному, проход 2 дал low).

Push в ветку эпика теперь назван при выборе стратегии: `flows/epic.yaml:34-36` «which the lead creates on the / remote and, after every step, brings the base into and pushes». Reset защищён: `epic-files:97` «only when `git log origin/<base>..HEAD` lists no commit».

## Известные дыры

| Известная дыра | Находка или как кит с ней справляется |
|---|---|
| 1. Красная проверка возвращается без учёта окружения | правило осталось (`epic-files:108` «First tell whether the work caused it»), но отказы push в ветки самого кита остались без правила (F3.2), а закрывающий PR неоднозначен (F5.1) |
| 2. Работа вне flow, merge без гейта | правка после аудита ждёт гейта: `supervisor.md:86` «reaches the base with the next gate's plan pull request». Коммит во время `readiness` — F12.1 |
| 3. Путь вне worktree прогона | новых путей нет; отсоединённая проверка идёт в общем worktree прогона (F12.1) |
| 4. Вердикт без порога важности | без изменений: `agents/delivery.md:48` «**A gap** is:», `plan_review` «Report `ok` when no finding is blocking» |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | без изменений: ни одной роли не добавлен навык |
| 6. Команды не совпадают с `expects.commands` | без изменений: `expects commands: git`, инструмент хоста — из `sdlc-settings` |
| 7. Read-only роли не совпадают с тем, что делают | без изменений: `read-only roles: plan-critic`, `lado: ">=0.34"`, README про Codex CLI |

## Не прослежено

Нет. `--compare` печатает «same as the plan in BLUEPRINT.md». Изменения не добавили ни роли, ни шага, ни гейта, ни навыка. Новый исход `stale` прослежен в чертеже (`epic.close`, «Decisions of the interview»). Обе жёлтые меры обоснованы в разделе 4.

## Прежние находки

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F3.1 у `close` нет выхода | RESOLVED | `flows/epic.yaml:209` «outcomes: {closed: done, stale: readiness}»; `step-review:91` «report `stale`»; `epic-files:111-113`. Новое — F8.1, F5.1, F8.2 |
| F12.1 reset без условия | RESOLVED | `epic-files:97-100` «only when `git log origin/<base>..HEAD` lists no commit … never reset in `readiness`». Новое — F3.3 |
| F3.3 отказ push после squash | RESOLVED | `epic-files:95-96` удаление удалённой ветки; push всё равно fast-forward (F9.1) |
| F4.1 delivery на устаревшем дереве | RESOLVED | `agents/delivery.md:25-27` «**On the base**: … `git switch --detach origin/<base>`». Новое — F12.1 |
| F12.2 push в ветку эпика без «да» | RESOLVED | `flows/epic.yaml:34-36` «creates on the / remote and, after every step, brings the base into and pushes» |
| F12.3 создание ветки эпика | RESOLVED | то же место |
| F5.1 правка после аудита без гейта | RESOLVED | `agents/supervisor.md:85-86`. Новое — F12.1 |
| F3.2 пауза без пути в `review` | RESOLVED | `flows/epic.yaml:136-139`. Новое — F3.1 |
| F8.1 повторный `readiness` без отметок | RESOLVED | `flows/epic.yaml:166-167` «first mark each / gap of your previous `delivery-report` RESOLVED or STILL OPEN» |
| F2.1 папка эпика после потери контекста | RESOLVED | `agents/supervisor.md:27-30` «the folder whose `plan.md` State names this run» |
| F6.1 копия правила двух шагов | RESOLVED | `flows/epic.yaml:135` |
| F6.2 правило checkout дважды | RESOLVED | `agents/supervisor.md:48-49` «but for the one exception `epic-files` names» |
| F6.3 правило для чужих ролей | RESOLVED | фраза удалена, `grep -n "Every role of this kit" agents/supervisor.md` пусто |
| F11.1 только `gh` | RESOLVED | `epic-files:92` «with the code host and its tool from `sdlc-settings` (**Git**)» |

## Вырезанные правила

| Удалённое правило (файл:строка на e9b37a0) | Где оно сейчас |
|---|---|
| `flows/epic.yaml:134` «(or two, only when they touch different files)» | `skills/step-brief/SKILL.md:57-58` «Two steps at once», строже |
| `agents/supervisor.md:46-47` «the one exception is the fast-forward pull `lead-sdlc` makes after a run, on its conditions» | `skills/epic-files/SKILL.md:126-127` «except the / fast-forward pull `lead-sdlc` makes after a run, on its conditions» |
| `agents/supervisor.md:47` «Every role of this kit keeps the safety rules of `epic-files`.» | `agents/delivery.md` «keep the safety / rules of `epic-files`»; plan-critic read-only |
| `skills/epic-files/SKILL.md:89` «Before the first change after a merge, put the run's branch on the base…» | `skills/epic-files/SKILL.md:95-100`, с условием |
| `skills/epic-files/SKILL.md:105-106` «a refused / push or merge» среди причин вне работы | сужено до «a refused push to a branch the kit does not own or a refused merge» (`:113-114`). Отказы push в ветки кита, кроме `stale`, потеряны — F3.2 |
| `flows/epic.yaml:195-196` (`close`) «A push, a red CI or a refused merge goes as `epic-files` says» | `flows/epic.yaml:201-202` «Any other failed push, red CI or refused merge» |
| `skills/step-review/SKILL.md:91` «tell the human» | заменено на «report `stale`»; человеку о причине не говорит никто — F7.1 |

## Пропущено раньше

- **F6.4** [low] `agents/supervisor.md:80`, против `flows/epic.yaml:146`
  >   they want the full answer, offer the readiness check: at the next review the steps left
  >       when the human asks for the readiness check with steps left, which then go to
  Правило «проверка готовности по просьбе человека, оставшиеся шаги в "Later"» записано и в роли, и в `do` шага `review`.
  Fix: в роли оставить только предложение и ссылку на `review`. Passes: full pass, confirmed.

## Оставлено планом

Нет: план исправляет все 14 находок и ничего не оставляет («Leave: none»).

## Вопросы человеку

Нет.

## Найдено по пути

- Имя файла отчёта: в `improve` отчёт после изменения должен быть другим файлом, но версия (0.1.0) и дата совпадают с отчётом `assess`. Поэтому файл переписан, а версия `assess` лежит в git (256b4c4) и в артефакте `assessment`.
