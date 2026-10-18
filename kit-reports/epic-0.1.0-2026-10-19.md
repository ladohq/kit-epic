# Отчёт о ките: epic 0.1.0

- Дата: 2026-10-19
- Кит: корень worktree прогона `create/epic` (`.lado/worktrees/kit-epic/create-epic`), путь задан шагом
- Коммит: 42fe6f6
- Оценил: критик kit-builder (слои a и b)
- Режим: полная оценка, первый визит `evaluate` прогона `create` (предыдущего отчёта нет)
- Проходы: проход 1 (kit.yaml и роли) сделал сам критик; проходы 2 (flow по состояниям) и 3 (навыки, потом роли) сделали два свежих независимых субагента. Им дали рубрику, папку кита и зависимые навыки: `sdlc-settings`, роли `supervisor` (это lead-sdlc) и `analyst`, flow `task` из sdlc 0.5.0, `tracker` из tracker-yougile. Отброшено 5 находок одного прохода, 5 оставлены как подтверждённые.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3; обе меры обоснованы в BLUEPRINT.md, раздел 4 |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 15 находок (5 high, 8 medium, 2 low); без находок 5 критериев из 12 |
| Охват | полная оценка всего кита, 9 файлов |

Счёт относится только к тому, что названо в строке «Охват».

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
| Words in a role prompt | agents/delivery.md | 379 | 800 / 1500 | green |
| Words in a role prompt | agents/plan-critic.md | 344 | 800 / 1500 | green |
| Words in the lead's prompt | agents/supervisor.md | 747 | 1000 / 1500 | green |
| Own skills | kit | 3 | 5 / 10 | green |
| MCP servers | kit | 0 | 2 / 4 | green |

## Similar paragraphs (one rule, one place; 55% similar or more)

none

Overall: yellow
```

### Flows

![epic](epic-0.1.0-2026-10-19/epic.svg)

```
same as the plan in BLUEPRINT.md
```
(код выхода 0)

## Исправить в первую очередь

1. Перед тем как считать красную проверку виной работы, отделять сбой окружения: красный CI PR плана, отказ merge, отсутствующий инструмент в проверках delivery. Сейчас `run` и `close` не знают, что делать, а у delivery такой сбой превращается в `gaps` (F3.1).
2. Запретить delivery пушить в ветку эпика до гейта, в том числе разрешения конфликтов, которые человек не видел. Пушить в `close` после `delivery_ok` (F12.1).
3. Записывать ответы человека на гейтах `plan_ok`, `step_ok` и `delivery_ok`, включая решения открытых вопросов «как скажете», в `decisions.md`. Сейчас они живут только в заметке для следующего шага (F2.1).
4. Не сообщать заново о пробеле, который человек уже перенёс в «Later»: иначе `readiness` ↔ `review` крутится до `max_visits` (F3.3).
5. Устанавливать предыдущую версию для проверки обновления только в свою папку или контейнер, а не системно на машине человека (F12.2).

## Находки

### 1. Границы ролей

Права указаны у каждой роли: plan-critic — «You change no file and commit nothing» и `read_only: true`, delivery — «you change no product code», ведущий — «you write the epic's files under `docs/epics/<key>/` and nothing else of the product». Противоречия в этих правах вынесены в критерий 5 (F5.1, F5.2, F12.1).

### 2. Передача между шагами

- **F2.1** [high] `flows/epic.yaml:91` (state `run`)
  > Carry out the step(s) the human just approved. The note is the human's answer at the
  > gate: a comment binds like the plan.
  На `plan_ok` человек утверждает «its open questions as recommended (or as you say)», то есть решает открытые вопросы. Комментарий с гейта по правилам LADO доходит только до следующего шага. Ни `run`, ни `close` не пишут ответ гейта в `decisions.md`: grep `decisions` по `run` и `close` ничего не находит. Отклонение на `step_ok` тоже не записывается (строки 129–130). Поэтому решение «как скажете» не попадёт ни в план следующих шагов, ни в итоговую таблицу решений (R5), ни в контекст после рестарта.
  Fix: в `run`, шаг 0: «Record the human's answer at the gate in `decisions.md` (each open question with its decision) and update `plan.md` where the answer changes it; commit». То же для отклонения на `step_ok` в `review` и для `delivery_ok` в `close`. Passes: 1/3, confirmed. Проверено: `grep -n decisions flows/epic.yaml` находит только строки 33, 38, 53, 57, 118, 119 (intake, plan, review), в `run` и `close` ни одной.

### 3. Когда шаг готов и какой исход

- **F3.1** [high] `flows/epic.yaml:93` (state `run`; также `close` :172, `agents/delivery.md:24`)
  > 1. Merge the approved plan change: push the run's branch, open its pull request to
  Ничего не сказано про красный CI, отказ хоста в merge или упавший push. У `run` и `close` по одному исходу. Delivery «Run the product's checks of **Checks** on the branch that will / be released», и красная проверка, даже из-за отсутствующего инструмента или Docker, доступна ему только как `gaps`, то есть как новый шаг плана (известная дыра 1). Ведущий импровизирует, а сбой окружения превращается в «шаг эпика».
  Fix: одно правило в `epic-files`. Сбой не из-за работы (сеть, инструмент, защита ветки, нестабильный CI) — ведущий показывает человеку команду и вывод и ждёт. Delivery шлёт его ведущему через `send_message`, это не пробел. Красный CI, который вызвал сам PR плана, ведущий чинит в worktree прогона. Passes: 3/3 (влияние high по проходу 2, проходы 1 и 3 дали medium).

- **F3.2** [medium] `flows/epic.yaml:153` (state `readiness`)
  > feature needs is missing; `gaps` otherwise: note_summary is the verdict; note_body
  У вердикта готовности нет порога. Неясно, считать ли пробелом обновление, которое здесь нельзя запустить, открытую проверку, перенесённую в релизный чеклист, мелкую тему документации. Сравните с plan-critic, где уровень blocking определён. Два worker'а дадут на одно и то же состояние `ready` и `gaps` (известная дыра 4).
  Fix: определить пробел: пункт «done» брифа или тема чеклиста без шага, красная проверка по вине работы, обновление, которое теряет файлы пользователя. Явно сказать, что неисполнимое здесь обновление и открытые проверки — не пробел. Passes: 3/3.

- **F3.3** [high] `flows/epic.yaml:123` (state `review`, с `agents/delivery.md:33`)
  > `gaps`, its `delivery-report` names what is missing: each gap becomes a step or goes
  > to "Later" on the human's word.
  Когда человек отправляет все пробелы в «Later», шагов не остаётся, `review` сообщает `finish`, и `readiness` проверяет «again from the start». Delivery смотрит «What the feature needs for full use against the brief's "done" … covered by which step, or missing» и ничего не знает про «Later». Тот же пробел снова даёт `gaps`, и цикл идёт до `max_visits: 3`, после чего прогон стоит на лимите вместо гейта.
  Fix: в `delivery.md`, Missing: «an item the human moved to "Later" (plan.md) is not a gap; list it under Later in the report». Passes: 1/3, confirmed. Цитата `delivery.md:33-34` покрывает только «covered by which step, or missing», а слово `Later` в `agents/delivery.md` grep не находит.

- **F3.4** [medium] `flows/epic.yaml:73` (state `plan_review`)
  > visit the human decides on what is still open, so on the third visit put each open
  > finding as a question for the human.
  > Done when every finding has an ID, a severity, its place and a quote. Report `ok`
  > when no finding is blocking; `changes` otherwise. note_summary is the verdict and
  Если на третьем визите остаются blocking, правило исхода даёт `changes`. Тогда `plan`, а четвёртый визит упирается в `max_visits`, и вопросы так и не доходят до `plan_ok`, где их должен решать человек. Отклонение на `plan_ok` тоже тратит визит `plan_review`.
  Fix: «On the third visit report `ok`, with each open finding under "Questions for the human"». Passes: 3/3.

- **F3.5** [medium] `flows/epic.yaml:20` (state `intake`)
  > branch: an epic of the same feature may exist already (then ask the human
  > whether this run continues it).
  Не сказано, что делать при «да»: перезаписывать `brief.md` и `decisions.md` или опираться на них, отменять ли этот прогон, если эпик ведёт другой открытый прогон. Есть риск стереть память существующего эпика.
  Fix: «If it continues: keep the key and extend the existing files, keeping every row of `decisions.md`; if an open `epic` run holds it, recommend cancelling this run». Passes: 2/3.

### 4. Независимая проверка

План проверяет свежий `plan-critic` до `plan_ok`, работу шагов проверяет `sdlc` (reviewer и свои гейты), готовность — отдельный `delivery`. Гейты читают то, что утверждают: `plan_ok` reads `[epic-brief, plan, plan-review, step-brief]`. Непроверенный push разрешения конфликтов — F12.1.

### 5. Противоречия

- **F5.1** [medium] `agents/supervisor.md:15`
  > you write the epic's files under `docs/epics/<key>/` and nothing else of the product.
  Противоречит `agents/supervisor.md:79` («`decisions.md` and move its risk into the product's release checklist as a check»), а также `skills/step-review/SKILL.md:67` и `:51`. Релизный чеклист — файл продукта. И не сказано, как такая правка попадает в базу.
  Fix: в `epic-files`, «Changing the files», назвать релизный чеклист единственным файлом продукта, который правят ведущий и delivery, и сказать, что он идёт в PR плана. Passes: 3/3.

- **F5.2** [medium] `agents/supervisor.md:44`
  > files"). Your own repository is the human's checkout: read it, never edit, commit in or
  > switch its branch there.
  lead-sdlc (sdlc 0.5.0 `agents/supervisor.md:59-61`) велит тому же ведущему после каждого `done`: «bring the base in with `git pull --ff-only`». `epic-files:100` при этом говорит «The human's checkout stays on its branch, untouched.» Какое правило главнее, не сказано, и ведущие поведут себя по-разному.
  Fix: одна фраза в «Where you write»: fast-forward pull по lead-sdlc разрешён (или пропускается). Passes: 2/3 (влияние medium по проходу 3, проход 2 дал low).

- **F5.3** [medium] `agents/supervisor.md:76`
  > they want the full answer, recommend finishing the current step and reporting `finish`
  Условие исхода в `flows/epic.yaml:128`: «Report `finish` when the plan has no step left». Роль советует `finish` при оставшихся шагах, `do` такого не разрешает. Если ведущий всё же даст `finish`, delivery отметит оставшиеся шаги как `gaps`, и выйдет круг F3.3.
  Fix: либо разрешить в `review` «`finish` also when the human asks for a readiness check; the steps left go to "Later" on their word», либо убрать совет из роли. Passes: 1/3, confirmed. Обе цитаты на месте (supervisor.md:76, epic.yaml:128).

- **F5.4** [medium] `skills/step-brief/SKILL.md:36`
  > remote." `sdlc` targets an epic branch only when the task's epic has one, so the
  Это неполная цитата аналитика sdlc 0.5.0, `agents/analyst.md:28-29`: «or the epic's branch when the tracker has epics and the task's epic has a branch on the remote». Первое условие выпало, в README:62-63 тоже. С трекером без эпиков буквальное правило аналитика даёт базу, и защита «отклонить на `design_ok`» может зациклить design. Обход ветки эпика держится на допущении, которого текст sdlc не даёт.
  Fix: процитировать условие целиком в step-brief и README, а в README «Known limit» сказать, что с трекером без эпиков вариант ветки эпика, скорее всего, не сработает до правки sdlc. См. вопрос 1. Passes: 1/3, confirmed. Цитата `analyst.md:28-29` выше.

### 6. Дублирование

- **F6.1** [low] `agents/supervisor.md:78`
  > - **Advice they did not take** (a release before a step you recommended): record it in
  Повторяет правило `skills/step-review/SKILL.md:69` («3. Advice the human did not take before the release: its risk goes into the release»), а не просто ссылается на него.
  Fix: оставить «record it in `decisions.md` (`step-review`, "A release between steps")». Passes: 2/3.

### 7. Когда звать человека

Решения человека проходят через три гейта и ведущего, intake спрашивает раундами, plan-critic выносит «Questions for the human», workers при нехватке настроек пишут ведущему (`sdlc-settings`: «stop and send the supervisor»). Правила для сбоя окружения нет — это F3.1, сходимость ревью плана — F3.4.

### 8. Циклы при повторном визите

Каждое состояние на цикле говорит, что меняется: `plan` («On a later visit the note says why you are back»), `plan_review` (RESOLVED / STILL OPEN, свой `plan-review` в produces), `review` (`retro` целиком, причина отклонения), `readiness` («check again from the start»). Циклы ограничены `max_visits: 3` или гейтом. Исход третьего визита — F3.4.

### 9. Краткость и зачем

Воды и истории в промптах нет, у неочевидных правил есть причина: «so the state is on the base for the next start», «Its analyst knows nothing of the epic except what the brief says». Все роли укладываются в зелёный объём.

### 10. Описания навыков

- **F10.1** [low] `skills/step-review/SKILL.md:25`
  > | the run's talk with the human | `lado log <session> --agent <worker>` for each worker of the run (`list_agents`), and your own messages about the run | the analyst's questions, rejections at gates, trouble in the manual check, leftovers named at the end |
  Каждый `review` запускает `lado`, но в `expects.commands` есть только `git` (известная дыра 6a). Это важно, только если `lado` может не оказаться в PATH агентов.
  Fix: добавить `lado` в `expects.commands` и в README или написать «when `lado` is not on PATH, mark the source unavailable». Passes: 2/3.

Описания `step-brief` и `step-review` ссылаются друг на друга («for reviewing a step that ended use `step-review`»). `tracker` и `sdlc-settings` объявлены в `expects.skills`, README называет кит sdlc и киты трекера.

### 11. Нейтральность к провайдеру

- **F11.1** [medium] `skills/epic-files/SKILL.md:92` (также `flows/epic.yaml:54`, 35, 120)
  > - After a commit to a file that is also an artifact (`plan`, `epic-brief`,
  Ведущий начинает работу в checkout человека («Your own repository is the human's checkout»), а `do` говорят «write `plan` from `plan.md`». Относительный `file` в `write_artifact` разрешается от checkout, где `docs/epics/<key>/plan.md` отсутствует или устарел, и гейт показывает старый текст. `lado-kit-format` требует абсолютный путь в worktree прогона (известная дыра 3).
  Fix: в этом пункте добавить «the lead passes the absolute path in the epic run's worktree (`flow_status`)». Passes: 1/3, confirmed. Правило `lado-kit-format`, «Artifacts»: «the lead passes the absolute path in the run's worktree (`flow_status`)». В ките этой фразы нет: grep `absolute` по `skills/ agents/ flows/` пуст.

В остальном инструментов, команд и моделей конкретных CLI нет. README: «The role `plan-critic` is read-only, so the kit does not run on Codex CLI.»

### 12. Безопасность и рамки

- **F12.1** [high] `skills/step-review/SKILL.md:77` (вызывается в `review` и в `readiness`, с `agents/delivery.md:11`, `:21`)
  > `git push origin HEAD:<epic branch>`, then `git switch <the run's branch>`. On a conflict,
  Delivery по `delivery.md:21` («the checks, and list each file and how you resolved it in the report; a conflict that») сам разрешает конфликты в коде продукта и пушит их в общую ветку эпика без гейта, с правилом «Never force-push». Человек видит разрешение только на `delivery_ok`, уже после push, а `delivery.md:11-12` говорит «you change no product code». Ошибочное разрешение не откатить, последующие прогоны `task` строятся на нём.
  Fix: в `readiness` делать merge локально, разрешение описывать в `delivery-report`, пушить в `close` после `delivery_ok` и назвать это в ask гейта. Строку 11 поправить: «no product code beyond resolving merge conflicts». Passes: 3/3 (влияние high по проходу 2).

- **F12.2** [high] `agents/delivery.md:28`
  > 4. **Upgrade.** Install the previous released version as the product's install docs say,
  Документация по установке часто ставит системно: бинарь в PATH, глобальные образы, сервис, данные в домашней папке. Так можно перезаписать установку и данные самого человека без его согласия. Ограничение `delivery.md:39` («Work only in the run's worktree and in folders you create for the upgrade») конфликтует с «as the product's install docs say», и где создавать папки, не сказано.
  Fix: ставить только в префикс, папку или контейнер, которые создал сам worker (во временной папке). Если документация допускает только системную установку, не запускать её, а написать в отчёте, что должен выполнить человек. Passes: 3/3 (влияние high по проходу 2).

Merge PR плана и закрывающего PR, создание задач трекера идут только после гейтов: «The lead then merges the plan's pull request after green CI, creates the step's tracker task and starts its run of `task`».

## Известные дыры

| Известная дыра | Находка или как кит с ней справляется |
|---|---|
| 1. Красная проверка возвращается без учёта окружения | F3.1 |
| 2. Работа вне flow, merge без гейта | merge — только после гейтов (ask `plan_ok`, `step_ok`, `delivery_ok`); «Start a run of `task` only for a step the human approved at `plan_ok` or `step_ok`». Push в ветку эпика без гейта — F12.1; правка релизного чеклиста вне шага — F5.1 |
| 3. Путь вне worktree прогона | F11.1 (относительный `file` ведущего), F12.2 (папки для обновления). Иначе: «Edit and commit only in the epic run's worktree (the path `flow_status` gives)» |
| 4. Вердикт без порога важности | plan-critic: «Report `ok` when no finding is blocking» + уровни в `plan-critic.md:46-48`; для `readiness` — F3.2 |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | справляется: plan-critic не получает `tracker`; `sdlc-settings` только пишет ведущему; plan-critic: «You change no file and commit nothing» |
| 6. Команды не совпадают с `expects.commands` | (a) F10.1 (`lado`); (b) `gh` только условно («with `gh`: …»), в expects его нет; (c) `expects commands: git` при `lado: ">=0.34"`, предупреждения нет |
| 7. Read-only роли не совпадают с тем, что делают | (a) единственная проверяющая роль, `plan-critic`, имеет `read_only: true`; (b) `delivery` коммитит и не read-only; (c) `dependencies.lado: ">=0.34"`; (d) README: «does not run on Codex CLI» |

## Не прослежено

Нет. Каждая роль, шаг, гейт и навык названы в разделе 3 чертежа с требованиями R. Обе жёлтые меры (7 шагов, 3 гейта) обоснованы в разделе 4. `--compare` печатает «same as the plan in BLUEPRINT.md».

## Вопросы человеку

1. Вариант «ветка эпика» не проверен, и по тексту sdlc 0.5.0 он, скорее всего, не работает с трекером без эпиков (F5.4). Оставить его в 0.1.0 с предупреждением в README или убрать до правки sdlc (явная целевая ветка, задача для kit-sdlc)? Рекомендация: оставить, но процитировать условие аналитика целиком и предупредить, что с трекером без эпиков вариант сейчас не работает. Задачу на kit-sdlc завести.

## Найдено по пути

Нет.
