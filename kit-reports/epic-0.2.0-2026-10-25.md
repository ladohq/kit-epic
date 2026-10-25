# Отчёт о ките: epic 0.2.0

- Дата: 2026-10-25
- Кит: корень worktree прогона `improve/epic-2` (`.lado/worktrees/kit-epic/improve-epic-2`), путь дан шагом
- Коммит: f5f4faa
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка относительно `kit-reports/epic-0.1.0-2026-10-25.md` и плана `plan` (база 7e79334, которую называет план), `git diff 7e79334 -- kit.yaml README.md BLUEPRINT.md agents flows skills`
- Проходы: три прохода по изменённому тексту и тому, что вокруг него (изменены все 10 файлов текста кита, поэтому фактически по всему киту). Проход 1 сделал сам критик (с ролей). Проходы 2 (с flow, по сценариям) и 3 (с навыков, затем роли и чертёж) сделали свежие независимые субагенты, у которых были diff, рубрика, `lado-kit-format`, зависимости `sdlc` 0.6.0 и исходники LADO. Проверку вырезанных правил критик провёл сам после проходов, по removed-строкам diff и таблице автора. Полный проход не делался: остались high-находки, вердикт `changes` («Re-evaluation» 5 нужен только перед `approved`). Находки одного прохода: отброшена 1, оставлено как подтверждённые 8.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3; обе меры обоснованы в BLUEPRINT.md, раздел 4. Промпт лида 990 слов (зелёный, у границы 1000) |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 15 находок в изменённом тексте (2 high, 11 medium, 2 low). Без находок 6 критериев из 12. Из 24 прежних находок 24 RESOLVED (F2.3 — с новой дырой, см. F2.1) |
| Охват | повторная оценка изменённого текста (10 файлов diff) без полного прохода: остались high-находки |
| Правило остановки | не выполнено: 2 high, 11 medium. Это совет для гейта релиза, а не запрет |

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
# Complexity budget: epic 0.2.0

| Measure | Where | Value | Green / yellow up to | Zone |
|---|---|---|---|---|
| Worker roles (not supervisor) | kit | 2 | 3 / 5 | green |
| Work steps in a flow | flows/epic.yaml | 7 | 5 / 8 | yellow |
| Gates in a flow | flows/epic.yaml | 3 | 2 / 3 | yellow |
| Words in a role prompt | agents/delivery.md | 594 | 800 / 1500 | green |
| Words in a role prompt | agents/plan-critic.md | 376 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 990 | 1000 / 1500 | green |
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

1. PR release checklist сливается в базу без «да» человека: назвать его в вопросе `plan_ok` или спрашивать перед мержем (F12.1).
2. Ветка рана после `plan_ok` больше не получает базу: `delivery` правит устаревший release checklist, закрывающий PR плана конфликтует, а правило разрешения конфликтов вырезано (F3.1).
3. Ответы intake, `plan_ok` и `delivery_ok`, принятые как рекомендовано, некуда записать: строка-счётчик есть только «per step» (F2.1).
4. `review`: «push nothing yet (`run` does)» противоречит пушу ветки эпика в `review` и не выполняется после `finish` (F5.1).
5. `status` пересобирается только на старте и конце рана шага, а должен показывать гейты `design_ok`/`merge_ok` посреди рана (F3.2).

## Находки

### 1. Границы ролей

Без находок в изменённом тексте. Права лида перечислены (`agents/supervisor.md:15-17`: «you write the epic's files under `docs/epics/<key>/` and the product's release checklist … and, on an epic branch, the base merges of»), `delivery` «push[es] nothing», `plan-critic` — `read_only: true`.

### 2. Передача между шагами

- **F2.1** [medium] `skills/epic-files/SKILL.md:79-80` (states `intake`, `run`, `close`)
  > - One counter line per step for the approvals as recommended, with their gate and
  >   question IDs: `S3: gates #40, #41, #43 — approved as recommended`.
  У одобрений вне шага строки-счётчика нет: раунды intake (`flows/epic.yaml:32-34`: «After each round add its / decisions to `docs/epics/<key>/decisions.md` (`epic-files`) and commit in the / run's worktree, so a lost context loses no answer.»), `plan_ok` (`run`, шаг 0) и `delivery_ok` (`close`, шаг 1). Лид либо снова пишет строку на всё (те 113 строк, которые убирает R5), либо ничего — и тогда ответы intake, данные «как рекомендовано», теряются с контекстом вопреки причине в intake. Исправление F2.3 из-за этого неполное.
  Fix: в `epic-files` — «approvals outside a step (intake rounds, `plan_ok`, `delivery_ok`) go on one epic counter line with their answers»; для intake — каждый ответ кратко, раз он больше нигде не хранится. Passes: 3/3

- **F2.2** [medium] `agents/supervisor.md:28-30`
  > 2. For each open `epic` run, find its folder on that run's branch (`flow_status` gives
  >    it): the folder whose `plan.md` State names this run (`Epic run:`), or the key in the
  >    run's `epic-brief`. Read its `plan.md` ("State" first) and `decisions.md` from the
  В `intake` нет ни `plan.md`, ни артефакта `epic-brief`, а закоммичен (по новому правилу, после каждого раунда) только `decisions.md`. После рестарта лид не найдёт папку, ответы которой уже сохранены, и спросит человека заново или заведёт второй ключ.
  Fix: «in `intake`, the folder the run's branch adds under `docs/epics/` (`git diff --name-only origin/<base>...<the run's branch>`)». Passes: 1/3, confirmed (проверено: `intake` пишет `brief.md` и `epic-brief` только в конце, `flows/epic.yaml:41-42`)

### 3. Готовность и исходы

- **F3.1** [high] `skills/epic-files/SKILL.md:130-131` (states `readiness`, `close`)
  >   branch>-checklist origin/<base>`, commit only that change, push, then `git switch <the
  >   run's branch>`. `delivery`'s commit at readiness goes with the closing pull request.
  Правки release checklist уходят в базу отдельными PR, но в ветку рана не возвращаются: базу в неё сливают только раз, «After the plan pull request of `run` merged» (`:137`). На readiness `delivery` переносит открытые проверки в копию checklist времён `plan_ok` (`agents/delivery.md:46-48`: «in the same commit as the report»): записи могут задвоиться, а закрывающий PR плана — конфликтовать с базой. Такой конфликт `epic-files:150-151` относит к «a refused push or merge» — причине вне работы, т. е. ошибку самого кита лид отдаёт человеку как сбой окружения (известная дыра 1); `stale` в `close` покрывает только ветку эпика. Правило разрешения конфликтов из 0.1.0 вырезано (см. «Вырезанные правила»).
  Fix: в `close` перед закрывающим PR (и перед `readiness`) — `git fetch origin`, `git merge origin/<base>` в ветку рана с прежним правилом конфликтов (`decisions.md` — строки обеих сторон, `plan.md` — более новая сторона, иначе `git merge --abort`); или правку checklist `delivery` тоже отдельным PR. Passes: 3/3 (уровни high и medium, взят high; также cut-rule check)

- **F3.2** [medium] `skills/epic-files/SKILL.md:101-105`
  > the phase; steps done N of M; the step in work with its run and that run's state; what
  > waits for the human, and where (the run and its gate, or the question); the open checks
  > until release; the next action. The lead writes it whole, rebuilt from the plan's State
  > and `flow_status`, at every move of the epic run and at every start and end of a step's
  `status` обязан показывать состояние рана шага и что ждёт человека, но пересобирается только на старте и конце рана. Гейты `design_ok` и `merge_ok` открываются посреди рана, так что `status` показывает устаревшее состояние и не показывает главное, что обещает R19, — что ждёт человека. В R19 чертежа есть «with a link», в навыке ссылки нет.
  Fix: «… and at every gate or question of a step's run», и «with a link» к гейту. Passes: 2/3

- **F3.3** [medium] `skills/step-review/SKILL.md:65` (state `review`)
  > - a new question for the human;
  Сам `review` рекомендует человеку ручную проверку (`step-review:27`: «if nobody did, recommend a manual check to the human») и «recommend checking that part now» (`:50`). Считается ли это «новым вопросом», не сказано: один лид отправит `next` после каждого шага с видимым изменением, другой — `continue`, и рекомендация уйдёт сообщением, которого нет в списке «Telling the human».
  Fix: одна фраза — рекомендованная ручная проверка является вопросом (`next`), или назвать её событием в «Telling the human». Passes: 2/3 (уровни medium и low, взят medium)

- **F3.4** [medium] `agents/supervisor.md:87-88`
  > - a step's run ended: its pull request, and "the next step started without a gate" or
  >   "waits at `step_ok`";
  Когда ран шага закончился, `review` ещё не выбрал `continue` или `next`, и для `finish` ни одна фраза не подходит. Лид либо гадает, либо шлёт строку после ревью — от прогона к прогону по-разному.
  Fix: «after the `review` of an ended step: its pull request, and "the next step started without a gate", "waits at `step_ok`" or "readiness check next"». Passes: 2/3 (уровни low и medium, взят medium)

- **F3.5** [medium] `skills/epic-files/SKILL.md:135-136`
  >   `gh api repos/<owner>/<repo>/rules/branches/<base>`). Then delete its branch on the
  >   remote when the host did not; after `close`, the epic branch too.
  Удаляется только ветка на remote, локальная `<the run's branch>-checklist` (`:129-130`, `git switch -c`) остаётся. Вторая правка checklist за эпик падает на `git switch -c` (ветка уже есть), и лид по «When a command fails» останавливается на человеке.
  Fix: `git switch -C`, или «and the local `-checklist` branch» в удалении. Passes: 1/3, confirmed (`git switch -c` на существующую ветку — ошибка git `a branch named … already exists`)

- **F3.6** [medium] `flows/epic.yaml:62` (state `plan`)
  > Turn each "missing" topic of `brief.md` into its step or open question. Then draft
  У intake нет вердикта для темы, на которую уже отвечает сама постановка («covered by S<n>» требует шага, `epic-files:32-34`), а `plan` не велено переписывать вердикт в `brief.md` на «covered by S<n>, Check: <line>». Критик блокирует всякую тему, которая не в этой форме (`plan-critic.md:21-23`), — блокирует или нет в зависимости от того, что лид случайно переписал.
  Fix: в `plan` — «and rewrite its verdict in `brief.md` as "covered by S<n>, Check: <line>"»; для intake — вердикт для уже отвеченной темы (например, «missing: <step to cover it>» по умолчанию). Passes: 1/3, confirmed

### 4. Независимая проверка

Без находок в изменённом тексте. `plan_review` (read-only критик) идёт до `plan_ok`, теперь с блокирующими признаками разбиения и релизной стороны (`plan-critic.md:49-50`: «a step to split, a readiness finding above»); после `continue` у шага остаются гейты `sdlc`; `readiness` свежим `delivery` до `delivery_ok`.

### 5. Противоречия

- **F5.1** [medium] `flows/epic.yaml:151` (state `review`)
  >       them, and write `plan` from `plan.md` again; push nothing yet (`run` does).
  Противоречит `step-review:95-97`, где лид в `review` пушит ветку эпика («**After every step** (the lead, in `review`)»), и правке checklist «at once» (`step-review:53`). После `finish` никакого `run` нет: коммиты последнего ревью и отчёт `delivery` живут только на этой машине до трёх визитов `readiness`, вопреки `epic-files:125` «so no commit lives on this machine alone».
  Fix: «push no plan change yet (`run` or `close` does; on `finish`, push the run's branch here); the epic-branch merge of `step-review` is pushed here». Passes: 3/3

- **F5.2** [medium] `flows/epic.yaml:110` (state `run`)
  >       a comment binds like the plan; after `continue`, the next step of the plan.
  С 21-го входа в `run` LADO открывает гейт предела, и после ответа `continue` нота шага — «continue: …», а ответ `step_ok` с комментарием — только в теле, как «Note before the gate» (`lado` `src/lado/runs.py`, `_answer_note`). Лид примет это за `continue` из `review` и по строке 113 («After `continue` there is no answer: record nothing.») не запишет ответ `step_ok` и может пропустить обязывающий комментарий.
  Fix: «after LADO's loop-limit `continue`, act on the note before the gate in its body, as after that gate». Passes: 1/3, confirmed (`runs.py:204`: «At a loop limit, continue enters the state anyway», `_answer_note` добавляет «Note before the gate: …»)

- **F5.3** [low] `BLUEPRINT.md:173`
  > - The run's branch is put on the base by `git reset --keep` only right after the plan
  Решение в чертеже противоречит построенному: `epic-files:138` «Never reset the run's branch». Также `BLUEPRINT.md:7` («and a report of every decision.») и R15 (`:103`, «a final report with every») противоречат новому R5, а строка `epic.run` (`:282`) всё ещё говорит «reports progress», хотя «Progress» убран.
  Fix: заменить решение на «never reset; one fast-forward after the plan pull request of `run`», «the decisions that matter» в `:7` и `:103`, убрать «reports progress». Passes: 1/3, confirmed

### 6. Дублирование

- **F6.1** [medium] `skills/step-review/SKILL.md:39` (state `review`)
  > - **Split** a step when its parts are independent and one blocks later steps.
  Второе правило разбиения, и оно отличается от нового (`epic-files:64`: «Split it when it has more than one capability the user will»). R20 чертежа (`BLUEPRINT.md:138`: «a step the review adds follows the same rule») не построен: шаг, добавленный на ревью, разбивается по старому правилу.
  Fix: «**Split** a step by the signs of `epic-files` ("plan.md"), a step the review adds too.» Passes: 1/3, confirmed

### 7. Когда звать человека

- **F7.1** [medium] `skills/step-review/SKILL.md:66` (state `review`)
  > - the human said "pause".
  Нигде не сказано, где лид хранит «пауза», сказанную человеком, пока шаг идёт: в `agents/supervisor.md` слова «pause» нет вовсе, а `flows/epic.yaml:127` («remove a pause from the State, and commit.») предполагает, что она в State. После потери контекста в `run` ревью паузы не увидит, отчитается `continue`, и следующий шаг стартует вопреки слову человека. Слово ещё и смешивается с «paused epic» (ожидание `sdlc`).
  Fix: в «When the human asks» — пункт «pause»: записать «Pause asked (<date>)» в State и закоммитить; `review` отчитывается `next`; `run` снимает её. Passes: 1/3, confirmed (`grep -n pause agents/supervisor.md` — пусто)

### 8. Петли на повторном визите

Без находок в изменённом тексте. `run` ограничен `max_visits: 20` со строкой на 20-м визите (`flows/epic.yaml:130-132`), поведение LADO на пределе совпадает с текстом (`runs.py`, `_enter`: каждый вход сверх предела снова открывает гейт); повторный `readiness` помечает пробелы RESOLVED / STILL OPEN и проверяет изменения (`:190-196`). Случай ответа гейта предела — F5.2.

### 9. Краткость и «почему»

- **F9.1** [low] `skills/epic-files/SKILL.md:139`
  >   yet (a row of `decisions.md`, a status) would be lost.
  «a status» теперь читается как новый артефакт `status`, который никогда не коммитится; имеется в виду статус шага в `plan.md`.
  Fix: «a step's status in `plan.md`». Passes: 1/3, confirmed

### 10. Описания навыков

Без находок. `epic-files` называет `status` и два PR плана, `step-review` — «what counts as a change of the plan»; пересечения со `step-brief` нет.

### 11. Нейтральность к провайдеру

Без находок. Только `git` и инструменты LADO; `gh` — по-прежнему пример («with `gh`:»).

### 12. Безопасность и рамки

- **F12.1** [high] `skills/epic-files/SKILL.md:128-129` (states `review`, `run`)
  > - A change of the release checklist must reach a release between steps, so it goes to the
  >   base at once by its own pull request: `git fetch origin`, `git switch -c <the run's
  Лид открывает и сливает в базу PR release checklist без гейта и без «да» человека: «да» требуется только для PR плана (`:132`: «Open the plan's pull request only after the human's yes at a gate.»), а вопрос `plan_ok` (`flows/epic.yaml:96-102`) этот PR не называет. В 0.1.0 такая правка ждала PR плана следующего гейта. Мерж в базу без «да» — известная дыра 2.
  Fix: назвать эти PR в вопросе `plan_ok` («and merges a change of the release checklist by its own pull request at once») или показывать человеку правку и мержить по его «да». Passes: 3/3 (уровни high и medium, взят high)

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка без учёта окружения | Закрыта: `epic-files:147` «First tell whether the work caused it»; красная проверка после слияния базы в ветку эпика идёт человеку (`step-review:98`). Открыто: F3.1 — конфликт, который вызывает сам кит, классифицируется как причина вне работы |
| 2. Работа вне flow, мерж без гейта | F12.1: PR release checklist сливается в базу без «да» |
| 3. Путь вне worktree рана | Закрыта: `epic-files:142` «passes `write_artifact` the absolute path in the epic run's worktree»; `supervisor.md:31` читает из ветки (`git show <the run's branch>:…`); intake читает `docs/epics/` из `origin/<base>` |
| 4. Вердикт без порога | Закрыта: `epic.yaml:87-88` «Report `ok` / when no finding is blocking»; `plan-critic.md:49-50` с новыми блокирующими; «**A gap** is» (`delivery.md:50`) |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | Закрыта: у `plan-critic` только справочные навыки (`epic-files`, `step-brief`, `sdlc-settings`) |
| 6. Команды не в ладу с `expects.commands` | Закрыта: `expects commands: git`; `gh` только «with `gh`:»; `lado log` — «without `lado` on PATH, mark it unavailable»; `>=0.34` |
| 7. Read-only роли не в ладу с работой | Закрыта: `read-only roles: plan-critic`, `>=0.34`, README «The role `plan-critic` is read-only, so the kit does not run on Codex CLI.» |

## Не прослежено

Ничего нового: новых ролей, шагов, гейтов и навыков нет; R18–R24 прослежены в разделе 3 чертежа и в обратной проверке; жёлтые меры обоснованы в разделе 4; `--compare` разницы не печатает. Расхождения текста чертежа с построенным — F5.3; R19 «with a link» и R20 «a step the review adds» построены не полностью — F3.2, F6.1.

## Прежние находки

Отчёт `kit-reports/epic-0.1.0-2026-10-25.md` и пункты плана.

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F1.1 права лида | RESOLVED | `supervisor.md:16-17` «and, on an epic branch, the base merges of / `step-review`» |
| F2.1 воркеры уже завершены | RESOLVED | `step-review:25` «named in `flow_status(run)` and the run's notes (`list_agents` no longer shows a finished worker» |
| F2.2 устаревший plan после рестарта | RESOLVED | `supervisor.md:31` «branch (`git show <the run's branch>:docs/epics/<key>/plan.md`)» |
| F2.3 ответы intake теряются | RESOLVED, с новой дырой | `epic.yaml:32-34` коммит после каждого раунда; но ответы «как рекомендовано» записать некуда — F2.1; папку после рестарта не найти — F2.2 |
| F2.4 архив на ветке эпика | RESOLVED | `step-review:22` «the archived change on the step's target branch» |
| F2.5 нота интегратора | RESOLVED | `step-review:24` «or the integrator's note at `merge`» |
| F2.6 пауза в State | RESOLVED | `epic-files:44` «paused (<why>)»; `epic.yaml:127` «remove a pause from the State» |
| F3.1 размер шага | RESOLVED | `epic-files:64-67` признаки; `plan-critic.md:31`, `:50` «a step to split»; `<S|M|L>` убран |
| F3.2 конфликт закрывающего PR | RESOLVED | `epic.yaml:240-241` «or the pull request from the epic branch into the base is red from the work or / conflicts with the base» |
| F4.1 «covered» без шага | RESOLVED | `epic-files:32-33` «covered by S<n>, Check: / <its line>»; `plan-critic.md:21-25` |
| F4.2 слияние в ветку эпика без проверок | RESOLVED | `step-review:96` «run the settings' **Checks**» |
| F5.1 коммит `.lado/sdlc.md` | RESOLVED | `epic-files:164-165` «and the commit of / `.lado/sdlc.md` that `lead-sdlc` makes there with the human's yes»; `supervisor.md:52` «the two exceptions» |
| F5.2 язык файлов и артефактов | RESOLVED | `epic-files:18-20` «An artifact written from a file … is the file as it is; notes, messages to the human and `status` are in / the chat language» |
| F6.1 условие `stale` в трёх местах | RESOLVED | `step-review:108-109` «`close` says when to report `stale`» |
| F6.2 предложение readiness | RESOLVED | `supervisor.md:99` «offer the readiness check (`review`)» |
| F7.1 `continue`/`cancel` в `close` | RESOLVED | `epic.yaml:244-247` «recommend `continue` when a fresh readiness / check can rebuild the result, `cancel` otherwise» |
| F8.1 предел readiness после отказа | RESOLVED | `supervisor.md:105-107` |
| F9.1 «Progress» | RESOLVED | бывший пункт убран; `supervisor.md:79-94` «Telling the human» |
| F9.2 `sdlc` 0.5.0 | RESOLVED | `step-brief:36` «The analyst of `sdlc` targets»; README:74 |
| F9.3 no-op | RESOLVED | `step-brief:61` «Say in each brief that the other runs alongside.» |
| F12.1 ключ слишком поздно | RESOLVED | `epic.yaml:124-125` «at once mark the step "in progress (<KEY>)" in / `plan.md` and commit, so a restart creates no second task»; `supervisor.md:33-34` |
| F12.2 способ мержа | RESOLVED | `epic-files:133` «merge it with a merge commit, never squash or rebase»; `:136` «after `close`, the epic branch too» |
| F12.3 нумерация эпика | RESOLVED | `step-brief:49-51`; `step-review:28`; `delivery.md:30-32`, `:51` |
| F12.4 чтение `docs/epics/` | RESOLVED | `epic.yaml:20-21` «`docs/epics/` of / `origin/<base>` after `git fetch origin`» |

Изменения плана (R18, R19, R10, R5, R8, R23, R24, BACKLOG, версия и README): построены (`review` `{continue: run, next: step_ok, finish: readiness}`, `run` `max_visits: 20`, `epic-files` «status», «decisions.md», «Changing the files», `supervisor.md` «Telling the human» и вопрос о дефекте, `step-review` «What changes the plan» и источник «checked by hand», `readiness` повторный визит, BACKLOG без «epic 0.1.1» и с `gate_when`, `kit.yaml` 0.2.0, README «Changes in 0.2.0»). Неполнота — F2.1, F3.1–F3.4, F5.1, F6.1, F7.1, F12.1.

## Вырезанные правила

| Вырезанное правило (файл:строка в 7e79334) | Где оно сейчас |
|---|---|
| `plan-critic.md:28-29` «A step too large to design in one round of questions, or two independent parts in one step» | `epic-files:64-67` признаки разбиения (`plan-critic.md:31` ссылается) |
| `plan-critic.md:21-22` «a "not relevant" without a reason» | `plan-critic.md:22-23` «nor "not relevant" with a reason» |
| `supervisor.md:57-58` запись: вопрос, рекомендация, ответ, принята ли | таблица `epic-files:88` (по новому R5 — только строки, что важны; см. F2.1) |
| `supervisor.md:69-70` задача, рождённая в шаге: «on the human's yes, linked as derived» | `step-brief` стоящие правила («A task born in the step is created by the step, on the human's yes, linked to this / task as derived») |
| `supervisor.md:77-78` «Where an epic stands» | `supervisor.md:81` «asked "where are we", point to it» |
| `supervisor.md:85-88` аудит: «it reaches the base with the next gate's plan pull request», «make the change once `readiness` ends» | `supervisor.md:103-104` «Change the worktree only while / the run is in your step or at a gate, not in `readiness`» (путь в базу — по R10, при `close`) |
| `epic.yaml:26-27` intake: вердикт по каждой теме | `epic-files:32-34` |
| `epic.yaml:38-39` intake: «with every decision of the rounds» | `epic.yaml:32-34` (по новому R5; см. F2.1) |
| `epic.yaml:102-104` `run` шаг 0: «each open question with its decision, and whether your recommendation was taken» | `epic-files:77-80` строка или счётчик |
| `epic.yaml:111-112` «this gate is the human's "yes, create"» | `epic.yaml:122` «(the human's yes at `plan_ok` or `step_ok` is the yes to create)» |
| `epic.yaml:170-175` `readiness`: «check again from the start, not only the gaps», после `stale` — «check again from the start» | заменено решением R24 (`epic.yaml:190-196`), человек одобрил |
| `epic-files:62-63` «record a decision even when it agrees with the recommendation» | заменено решением R5 (`epic-files:77-83`), человек одобрил |
| `epic-files:85-88` «where a moved check goes with the next plan change» | `epic-files:128-131` отдельный PR сразу (без «да» — F12.1) |
| `epic-files:89-91` «so the state is on the base for the next start»; «only after the human's yes at a gate» | `epic-files:126-127` состояние в ветке рана на remote; `:132` «Open the plan's pull request only after the human's yes at a gate» |
| `epic-files:95-96` удалить ветку рана на remote после PR плана | `epic-files:135-136` |
| `epic-files:97-100` `reset --keep` / merge базы, «never reset in `readiness`» | `epic-files:137-139` «Never reset the run's branch» |
| `epic-files:100-102` «On a merge conflict: in `decisions.md` keep the rows of both sides (newest last); in `plan.md` take the newer side and check it against `decisions.md`; then commit. In any other file `git merge --abort` and tell the human.» | **утеряно**: слияние базы в ветку рана осталось одно, «(a fast-forward)», а конфликт возможен теперь при закрывающем PR — F3.1 (Passes: cut-rule check) |
| `step-brief:58-59` «the second to merge brings the latest base in first» | интегратор `sdlc` делает это всегда (`integrator.md:29-30`) |
| `step-review:52-53` «fix the checklist with the plan» | `step-review:53-54` «fix it at once (`epic-files`, "Changing the files")» |
| `step-review:89-92` условие `stale` | `epic.yaml:238-242` (`step-review:108-109` ссылается) |

## Пропущено ранее

Нет: полный проход не делался (вердикт `changes`).

## Оставлено планом

Нет: план ничего не оставляет («Leave: none»).

## Вопросы для человека

1. PR release checklist посреди эпика (F12.1): разрешить лиду мержить его без отдельного «да», назвав это в вопросе `plan_ok`, или спрашивать каждый раз? Рекомендация: назвать в вопросе `plan_ok` — правка небольшая и нужна до релиза между шагами, а лишний вопрос противоречит цели LAD-145.
2. Конфликт release checklist при закрытии (F3.1): сливать базу в ветку рана перед `readiness` и `close` (возвращает часть правила конфликтов) или и правку `delivery` отправлять отдельным PR? Рекомендация: слияние базы перед `readiness` — `delivery` тогда видит актуальный checklist.

## Найдено по пути

Нет.
