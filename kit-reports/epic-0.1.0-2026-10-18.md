# Отчёт о ките: epic 0.1.0

- Дата: 2026-10-18
- Кит: корень worktree прогона `improve/epic` (`.lado/worktrees/kit-epic/improve-epic`), путь дан шагом
- Коммит: 49eb27c
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка, второй визит `evaluate`, относительно предыдущего отчёта (этот же файл, коммит 7619f57, кит на 6651bcf). База — коммит, который план называет: `git diff e9b37a0 -- kit.yaml README.md BLUEPRINT.md agents flows skills`. Последний цикл исправлений — `git diff 7619f57`.
- Проходы: три прохода по изменённому тексту и его окружению. Проход 1 сделал сам критик (по последнему diff по порядку). Проходы 2 (начало с `close`, `readiness`, `review`, `run`) и 3 (начало с `epic-files`) сделали свежие независимые субагенты; у них были оба diff, рубрика и зависимые тексты sdlc 0.5.0, но не прежний отчёт. Отдельный субагент параллельно сделал полный проход по 6 файлам, которые затронул diff; он воспроизвёл squash-слияние в scratchpad. Проверку вырезанных правил критик провёл сам, по `git diff --word-diff` и таблице автора. Находки одного прохода: отброшено 2, оставлена как подтверждённая 1. Находки полного прохода: 1 вынесена в «Пропущено раньше», остальные совпали с находками проходов.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3. Обе меры обоснованы в BLUEPRINT.md, раздел 4 |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 7 находок в изменённом тексте (2 high, 3 medium, 2 low), из них 1 STILL OPEN с прошлого раза (F3.1); без находок 6 критериев из 12. Из 9 прежних находок 8 RESOLVED, 1 STILL OPEN; в «Пропущено раньше» 2 |
| Охват | повторная оценка изменённого текста с полным проходом по 6 файлам, которые затронул diff |
| Правило остановки | не выполнено: 2 high, 3 medium. Это совет для гейта релиза, а не запрет |

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

1. Пауза: `step-brief` переписывается одной строкой, и возобновлённый шаг стартует без брифа, без рамки и без целевой ветки. Бриф шага надо сохранить под строкой паузы (F2.2).
2. `close` на лимите `readiness`: «do not report `stale` … stay in `close`» не оставляет выхода. Надо сообщать `stale`: LADO сам держит прогон на лимите, человек выбирает `continue` или `cancel` (F7.2).
3. Пауза: у статуса «planned» в шаблоне `plan.md` нет места для ключа, поэтому `run` всё равно создаст вторую задачу в трекере. Нужен статус `planned (<KEY>, paused)` (F3.1).
4. Исключение `stale` в `epic-files` стоит без условия «from the work» и в списке причин вне работы. Сбой CI посреди прогона подходит и под «no outcome», и под `stale`. Нужно условие или ссылка на `close` (F5.2).
5. Правило конфликта «keep the rows of both sides» годится для `decisions.md`, но не для `plan.md` (F3.4).

## Находки

### 1. Границы ролей

Новых находок нет. Аудит не трогает worktree, пока там работает delivery: `agents/supervisor.md:86-88` «Change the worktree only while / the run is in your step or at a gate». Все шаги `close` выполняет ведущий, delivery «push nothing».

### 2. Передача между шагами

- **F2.2** [high] `flows/epic.yaml:139` (state `review` → `step_ok` → `run`)
  >       `step-brief` whole as "No step proposed: the epic is paused (<why>); approving
  На паузе `step-brief` целиком заменяют этой строкой, и бриф шага S<n> пропадает. После «да» на `step_ok` шаг `run` запускает возобновлённый шаг по `step-brief` (`:114` «Start its run of `task` (`step-brief`, "Starting the step's run")»), то есть «with the task text: the tracker key, then the brief whole» (`skills/step-brief/SKILL.md:64`). Аналитик `task` получает строку о паузе: без рамки, порогов, постоянных правил и строки 5 о целевой ветке. А пауза случилась именно из-за целевой ветки, так что возобновлённый шаг снова нацелится на базу. Прогон идёт не туда.
  Fix: на паузе писать `step-brief` целиком так: строка паузы, под ней черновик задачи и бриф шага S<n> без сокращений. В `run` шаг 3 сказать, что возобновлённый шаг стартует с этим брифом. Passes: 2/3 и полный проход (проходы 2 и 3; проход 1 её пропустил).

### 3. Когда шаг готов и какой исход

- **F3.1** [medium] STILL OPEN (с прошлого раза; исправление неполное) `flows/epic.yaml:110`, с `skills/epic-files/SKILL.md:47`
  >       3. For each approved step, take its tracker key from `plan.md` when it has one (a
  > Status: planned | in progress (<KEY>, run <name>) | done (<KEY>, PR <link>) |
  Ключ ищут только в `plan.md`. Но пауза переводит шаг «back to "planned"» (`skills/step-brief/SKILL.md:74`), а у статуса «planned» в шаблоне нет места для ключа. Ведущий, который следует шаблону, потеряет ключ, и `run` шаг 3 всё равно создаст вторую задачу в трекере. Это ровно та ошибка, которую исправление должно было закрыть. Ask `step_ok` (`:157-158`) тоже по-прежнему обещает «creates the step's tracker task».
  Fix: в шаблон добавить `planned (<KEY>, paused)`, в step-brief «Wait for `sdlc`» — «back to "planned" with its key», в ask `step_ok` — «(or reuses the paused step's)». Passes: 3/3 и полный проход (влияние medium по проходам 2, 3 и полному, проход 1 дал low).

- **F3.4** [medium] `skills/epic-files/SKILL.md:100`
  >   never reset in `readiness`. On a merge conflict in the epic's files keep the rows of
  «Keep the rows of both sides» годится для `decisions.md`. Но у `plan.md` один блок State и одна строка Status на шаг. Если оставить обе стороны, получатся два `Phase:` или два статуса одного шага («in progress» и «done»). А State ведущий читает первым при каждом старте.
  Fix: «in `decisions.md` keep the rows of both sides (newest last); in `plan.md` take the newer side and check it against `decisions.md`; in any other file `git merge --abort` and tell the human». Passes: 2/3 (влияние medium по проходу 3, проход 2 дал low).

### 4. Независимая проверка

Новых находок нет. Ветка эпика вливается в базу только после `delivery_ok`, а «closed» появляется лишь после неё: `flows/epic.yaml:201-202` «This comes before step 3, / so the base never says "closed" for a result that is not on it».

### 5. Противоречия

- **F5.2** [medium] `skills/epic-files/SKILL.md:115`, против `flows/epic.yaml:213`
  > instead (`close`'s `stale` for the push of the readiness branch and the pull request from
  >       or the pull request from the epic branch into the base is red from the work: the
  Исключение для `stale` стоит в предложении о причинах вне работы, среди них «a flaky CI run», и не имеет условий. Буквально: случайный сбой CI на PR из ветки эпика в базу — это `stale`. А `close` говорит `stale` только при «red from the work». Для отклонённого push в `epic-files` нет условия «because the epic branch moved». Две копии правила уже расходятся. Лишний `stale` тратит один из трёх визитов `readiness`.
  Fix: в `epic-files` оставить только ссылку: «(`close`'s `stale`, as `close` says; `delivery`'s upgrade …)». Или повторить оба условия дословно. Passes: 2/3 (проходы 2 и 3; полный проход указал на то же расхождение копий).

### 6. Дублирование

Новых находок, кроме расхождения копий в F5.2, нет. Правило reset/merge одно (`epic-files:95-102`). Правило «закрыть после слияния» есть только в `close`.

### 7. Когда звать человека

- **F7.2** [high] `flows/epic.yaml:217-218` (state `close`)
  >       visits (`flow_status`), do not report `stale`: show the human the command and its
  >       output and stay in `close` until they decide.
  LADO сам держит прогон, когда состояние исчерпало `max_visits`, и человек выбирает `continue` или `cancel` (`lead-sdlc`, «A run waits at a loop limit»). На этот механизм кит опирается и в `plan` (`:64` «the next review reaches its limit: tell the»). Здесь путь запрещён, а у `close` нет исхода для решения человека. Push отклоняют, потому что ветка эпика ушла вперёд, а красный PR нельзя слить. Остаётся только `lado flow-set` или отмена. Путь достижим: третий визит `readiness` принудительно сообщает `ready` (`:174`) даже с красной проверкой, которую вызвала работа.
  Fix: «When `readiness` has had its three visits, report `stale` anyway: LADO holds the run at its limit; show the human the command and its output and recommend `continue` or `cancel`». Passes: 3/3 и полный проход (влияние high по проходу 2, проходы 1, 3 и полный дали medium; взято высшее).

### 8. Циклы при повторном визите

- **F8.3** [low] `flows/epic.yaml:200` (state `close`, повторный визит)
  >          (`step-review`, "Epic branch", "At close"), open the pull request from the epic
  После `stale` из-за красного PR этот PR из ветки эпика в базу остаётся открытым. На следующем визите `close` шаг 2 велит открыть его снова. Хост откажет: PR между теми же ветками уже есть. Отказ уйдёт в «When a command fails» как причина вне работы, к человеку.
  Fix: «open the pull request from the epic branch into the base, or reuse the one an earlier `close` opened». Passes: 1/3, confirmed. Путь `stale` по красному PR (`:213`) не закрывает PR, а шаг 2 его не переиспользует: в `close` нет ни слова «reuse».

### 9. Краткость и зачем

- **F9.2** [low] `flows/epic.yaml:214-215` (state `close`)
  >       readiness check is out of date and `delivery` checks again. First set the State of
  После перестановки `stale` возможен только в шаге 2, до того как шаг 3 пометит план закрытым. State за это время никто не менял, так что «set … back» — пустое действие. `git commit` упадёт с «nothing to commit», и агент может принять это за сбой команды.
  Fix: убрать предложение или сделать его условным: «when the State does not say `readiness`». Passes: 3/3.

Новая причина удаления удалённой ветки верна: `epic-files:96` «so no merged branch is left on the host».

### 10. Описания навыков

Новых находок нет. Описания не менялись, каждый навык из `skills:` используется.

### 11. Нейтральность к провайдеру

Новых находок нет. В новом тексте только `git` и инструменты LADO (`flow_status`).

### 12. Безопасность и рамки

Новых находок нет. Push и merge в `close` идут после гейта `delivery_ok`. Удалённую ветку прогона удаляют только после слияния одобренного PR. Причины вне работы снова включают «a refused / push or merge» (`epic-files:113-114`).

## Известные дыры

| Известная дыра | Находка или как кит с ней справляется |
|---|---|
| 1. Красная проверка возвращается без учёта окружения | `epic-files:108` «First tell whether the work caused it»; отказы push снова вне работы. Размыто только исключение `stale` (F5.2) |
| 2. Работа вне flow, merge без гейта | merge только после гейтов; правка после аудита ждёт гейта и не трогает worktree во время `readiness` (`supervisor.md:86-88`) |
| 3. Путь вне worktree прогона | новых путей нет |
| 4. Вердикт без порога важности | без изменений; третий визит `readiness` выносит пробелы в вопросы человеку (`:174-175`) |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | без изменений |
| 6. Команды не совпадают с `expects.commands` | без изменений: `expects commands: git` |
| 7. Read-only роли не совпадают с тем, что делают | без изменений: `read-only roles: plan-critic`, `lado: ">=0.34"` |

## Не прослежено

Нет. `--compare` печатает «same as the plan in BLUEPRINT.md». Новых элементов нет. Обе жёлтые меры обоснованы, число слов в разделе 4 совпадает со скриптом (819).

## Прежние находки

Находки предыдущего отчёта (7619f57):

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F8.1 `close` сливает «closed» до шага 2 | RESOLVED | `flows/epic.yaml:198` «1. Record their answer in `decisions.md` and commit»; шаг 3 после шага 2, `:201-202` |
| F3.1 пауза: старый бриф, вторая задача | STILL OPEN | бриф теперь переписывается, но ключ теряется в «planned» (`epic-files:47`) — см. F3.1 выше; исправление само породило F2.2 |
| F3.2 отказ push в ветки кита без правила | RESOLVED | `epic-files:113-114` «a refused / push or merge» |
| F5.1 «closing pull request» неоднозначен | RESOLVED | `flows/epic.yaml:213` «the pull request from the epic branch into the base is red from the work». Расхождение копии в `epic-files` — F5.2 |
| F8.2 нет правила на лимите `readiness` | RESOLVED | `flows/epic.yaml:174` «On the third visit report `ready` whatever is open». Новое в `close` — F7.2 |
| F12.1 коммит аудита в общем worktree | RESOLVED | `agents/supervisor.md:86-88` |
| F3.3 конфликт при merge | RESOLVED | `epic-files:100-102`. Не подходит для `plan.md` — F3.4 |
| F7.1 человеку не говорят причину `stale` | RESOLVED | `flows/epic.yaml:172-173` «quote the command and its / output at the top of the report» |
| F9.1 неверное «зачем» удаления ветки | RESOLVED | `epic-files:96` «so no merged branch is left on the host» |
| F6.4 (Пропущено раньше) | STILL OPEN | `agents/supervisor.md:80` без изменений; автор оставил её человеку |

Из отчёта `assess` (14 находок) все остаются RESOLVED (проверено в прошлом визите; последний diff их мест не трогал, кроме перечисленных выше).

## Вырезанные правила

| Удалённое правило (файл:строка на e9b37a0 или 7619f57) | Где оно сейчас |
|---|---|
| `flows/epic.yaml:134` «(or two, only when they touch different files)» | `skills/step-brief/SKILL.md:57-58` |
| `agents/supervisor.md:46-47` исключение fast-forward pull | `skills/epic-files/SKILL.md` «Safety on the human's machine», «on its conditions» |
| `agents/supervisor.md:47` «Every role of this kit keeps the safety rules» | `agents/delivery.md` «keep the safety / rules of `epic-files`» |
| `skills/epic-files/SKILL.md:89` (e9b37a0) reset после слияния | `skills/epic-files/SKILL.md:95-102`, с условием |
| `skills/epic-files/SKILL.md` (7619f57) «At `close`, a refused push … is `close`'s `stale`.» | `skills/epic-files/SKILL.md:115-116` в оговорке «unless the step or role says»; условия при переносе потеряны — F5.2 |
| `skills/epic-files/SKILL.md` (7619f57) «a refused push to a branch the kit does not own» | `:113-114` «a refused push or merge» (шире, правило e9b37a0 восстановлено) |
| `flows/epic.yaml` (7619f57) `close` 1 «mark `plan.md` closed … merge it as a plan change» | `flows/epic.yaml` `close` шаг 3 |
| `flows/epic.yaml` (7619f57) «closing pull request's CI is red from the work» | `:213` «the pull request from the epic branch into the base is red from the work» |
| `skills/step-review/SKILL.md:91` (e9b37a0) «tell the human» | `flows/epic.yaml:172-173`: причина цитируется в отчёте, его показывает `delivery_ok` |
| `flows/epic.yaml` (7619f57) `review` «the note says `step_ok` waits for `sdlc`, or the human cancels the run» | `flows/epic.yaml:140-141`, без изменений по смыслу |

Потерянных правил нет. Ослабленное при переносе условие `stale` описано в F5.2.

## Пропущено раньше

- **F2.3** [medium] `flows/epic.yaml:101-104` (state `run`)
  >       gate: a comment binds like the plan.
  >          whether your recommendation was taken; change `plan.md` where the answer changes
  Шаг 0 применяет обязательный комментарий человека с гейта только к `plan.md`. Но шаг 3 создаёт задачу в трекере «from `step-brief`» и запускает прогон с «the brief whole», а `run` не переписывает `step-brief`. Комментарий, меняющий рамку или проверку шага, не дойдёт до аналитика, если ведущий не догадается сам.
  Fix: в шаге 0 также применить ответ к черновику задачи и брифу и переписать `step-brief` до шага 3. Passes: full pass, confirmed. Строки 101–104 не менялись с e9b37a0.

- **F6.4** [low] STILL OPEN `agents/supervisor.md:80`, против `flows/epic.yaml:146`
  >   they want the full answer, offer the readiness check: at the next review the steps left
  Правило «проверка готовности по просьбе человека» записано и в роли, и в `review`.
  Fix: в роли оставить предложение и ссылку на `review`. Passes: full pass, confirmed (прошлый визит).

## Оставлено планом

Нет: план ничего не оставляет («Leave: none»).

## Вопросы человеку

Нет.

## Найдено по пути

- Имя файла отчёта совпадает с отчётом `assess` (версия 0.1.0, та же дата), поэтому файл переписывается на каждом визите. Прежние версии лежат в git (256b4c4 — assess, 7619f57 — первый evaluate) и в артефакте `assessment`.
