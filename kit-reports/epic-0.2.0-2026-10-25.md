# Отчёт о ките: epic 0.2.0

- Дата: 2026-10-25
- Кит: корень worktree прогона `improve/epic-2` (`.lado/worktrees/kit-epic/improve-epic-2`), путь дан шагом
- Коммит: 5f80dea
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка, пятый визит `evaluate`. Предыдущий отчёт — эта же оценка на 785b306 (`changes`, 7 находок). Базой изменения служит 7e79334, её называет план. Правка этого визита: `git diff 785b306`, коммиты d86a6b4 и 5f80dea.
- Проходы:
  - три прохода по правке d86a6b4 и тексту вокруг неё: проход 1 сделал критик, проходы 2 (с flow, с опытами в git) и 3 (с навыков) — свежие независимые субагенты;
  - проверку вырезанных правил критик сделал сам;
  - находку high критик воспроизвёл сам в git 2.39.5 (во временной папке, не в ките);
  - полный проход не делался: вердикт `changes`;
  - находки одного прохода: отброшена 1 (проход 3, «нота `next` недоступна в `run` после потери контекста»: проход 2 показал, что нота, которая привела к гейту, приходит с ответом гейта), оставлено как подтверждённые 2.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3; обоснованы в BLUEPRINT.md, раздел 4. Промпт лида 997 слов |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 4 новые находки в изменённом тексте (1 high, 1 medium, 2 low). Без находок 9 критериев из 12. Из 7 находок прошлого визита 6 RESOLVED, F12.3 — RESOLVED с новой дырой (F12.5) |
| Охват | повторная оценка правки d86a6b4 (5 файлов) без полного прохода: остался high |
| Правило остановки | не выполнено: 1 high, 1 medium |

Счёт относится только к тому, что названо в строке «Охват». Его нельзя сравнивать со счётом полной оценки.

**Вердикт: `changes`.** Новая проверка сброса в `intake` в самом частом случае (checkout человека на актуальной базе) не защищает от второго сброса, и тот стирает закоммиченные ответы (F12.5). Это воспроизведено.

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

1. Условие сброса в `intake`: сбрасывать, только пока в reflog ветки рана есть лишь строка её создания (`branch: Created from HEAD`) и `git -C <the run's worktree> log origin/<base>..HEAD -- docs/epics/` не показывает коммитов рана (F12.5).
2. Пауза: каждое «pause» — своя строка `Pause asked: <date time>`; нота `next` цитирует строку, `run` снимает только её (F3.9).

## Находки

Новые находки в правке d86a6b4. Номера продолжают отчёт на 785b306.

### 1. Границы ролей

Без находок.

### 2. Передача между шагами

Без находок. Нота, которая привела к `step_ok`, приходит в `run` с ответом гейта («Note before the gate»), так что проверка «a `next` whose note named that pause» выполнима.

### 3. Готовность и исходы

- **F3.9** [medium] `flows/epic.yaml:141` (state `run`)
  >          asked` only after `step_ok` approved a `next` whose note named that pause, never
  В State одна строка `Pause asked: <date>` (`skills/epic-files/SKILL.md:47`), и нигде не сказано, что делать со вторым «pause». Например, человек говорит «pause» снова на `step_ok`, уже открытом из-за первой паузы. Лид либо оставляет строку, либо переписывает её дату. Затем `run` снимает её как «ту, что назвала нота», и вторая пауза теряется. Обе обычно с одной датой, отличить их нельзя. Следующий `review` даёт `continue`, и шаг стартует без «да» человека.
  Fix: в `step-review` («What changes the plan») — каждое «pause» добавляет свою строку `Pause asked: <date time>`; нота `next` цитирует строку; `run` снимает только процитированную. Passes: 2/3

### 4. Независимая проверка

Без находок.

### 5. Противоречия

- **F5.11** [low] `flows/epic.yaml:26` (state `intake`)
  >          numbers, its State back to `plan`.
  Продолжаемый эпик сохраняет `plan.md`. Если отменённый ран оставил в State на базе `Pause asked`, по новому правилу («never after `plan_ok`») её ничто не снимет до первого ревью, и оно откроет `step_ok`, которого человек в этом ране не просил.
  Fix: при продолжении — убрать `Pause asked` из State или спросить человека, держится ли пауза. Passes: 1/3, confirmed

### 6. Дублирование

Без находок. Правило паузы теперь в одном месте (`run` шаг 3); `step-review:76` ссылается: «`run` says when it is removed (step 3)».

### 7. Когда звать человека

Без находок.

### 8. Петли на повторном визите

Без находок.

### 9. Краткость и «почему»

- **F9.3** [low] `flows/epic.yaml:182` (state `review`)
  >       kind of change with "no". Report `next` otherwise, always when the State has `Pause asked` (the note names it),
  Строка не переразбита и вылезает за ширину файла (117 символов). Заодно не сказано, как именно нота «names it» (см. F3.9).
  Fix: переразбить абзац; «(the note quotes the line `Pause asked`)». Passes: 1/3, confirmed

### 10. Описания навыков

Без находок.

### 11. Нейтральность к провайдеру

Без находок.

### 12. Безопасность и рамки

- **F12.5** [high] `flows/epic.yaml:30-32` (state `intake`)
  >          request would bring into the base. After a lost context, reset only when `git -C
  >          <the run's worktree> reflog show <the run's branch>` has no line `reset: moving
  >          to origin/<base>`: a second reset would erase the answers committed since.
  Когда checkout человека на актуальной базе (HEAD == `origin/<base>`, самый частый случай), первый `reset --keep` ветку не двигает и в её reflog ничего не пишет. После коммита ответов и потери контекста строки `reset: moving to origin/<base>` нет, правило велит сбросить, и сброс стирает закоммиченные ответы `decisions.md`. Они не запушены и остаются только в reflog. Вырезанная проверка «no commit under `docs/epics/`» (в 160a6c9 строка 29) теперь не защищает и от рана, который закоммитил ответы, не сбросив ветку.
  Воспроизведено (git 2.39.5): `git worktree add -b run1 ../wt HEAD` на актуальной базе → `git -C ../wt reset --keep origin/main` → `reflog show run1` печатает только `run1@{0}: branch: Created from HEAD` → коммит → в reflog нет строки reset (`grep -c` = 0) → повторный `reset --keep origin/main` → `log -1` показывает базовый коммит, коммита ответов в ветке нет. При HEAD, не совпадающем с базой, строка `run2@{0}: reset: moving to origin/main` есть.
  Fix: «reset only while `git -C <the run's worktree> reflog show <the run's branch>` has just its creation line and `git -C <the run's worktree> log origin/<base>..HEAD -- docs/epics/` shows none of this run's commits; never after». Альтернатива — `reflog show HEAD` в worktree рана: он записывает и сброс без сдвига. Passes: 3/3 (проходы 1 и 2 — high, проход 3 — medium по другой причине, тот же Fix; плюс cut-rule check)

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка без учёта окружения | Закрыта (без изменений) |
| 2. Работа вне flow, мерж без гейта | Закрыта: проверка сброса больше не зависит от чужих коммитов под `docs/epics/`, поэтому чужие коммиты HEAD человека сбрасываются (F12.3 прошлого визита) |
| 3. Путь вне worktree рана | Закрыта: `git -C <the run's worktree>` и для сброса, и для проверки |
| 4. Вердикт без порога | Закрыта (без изменений) |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | Закрыта (без изменений) |
| 6. Команды не в ладу с `expects.commands` | Закрыта: только `git` |
| 7. Read-only роли не в ладу с работой | Закрыта (без изменений) |

Ответы человека теряются при повторном сбросе — F12.5.

## Не прослежено

Ничего: новых элементов нет, жёлтые меры обоснованы, `--compare` совпадает. Решение §1 (`BLUEPRINT.md:173`, «except once at `intake`, before its first commit») совпадёт с текстом, когда F12.5 вернёт проверку «нет коммитов рана». Строка §5 (`:369`) правке соответствует.

## Прежние находки

Отчёт на 785b306.

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F12.3 [high] проверка сброса не отличает чужие коммиты | RESOLVED, с новой дырой | условие теперь по reflog ветки (`epic.yaml:30-32`), чужие коммиты не мешают; но сброс без сдвига в reflog не пишется — F12.5 |
| F12.4 [medium] проверка не в worktree рана | RESOLVED | `epic.yaml:27-28` и `:30-31` — `git -C <the run's worktree>` |
| F3.8 [medium] пауза по датам | RESOLVED | `epic.yaml:140-142` «only after `step_ok` approved a `next` whose note named that pause» (два «pause» подряд — F3.9) |
| F5.9 [medium] две копии правила паузы | RESOLVED | `step-review:76` «`run` says when it is removed (step 3)» |
| F5.10 [low] «`intake` puts its branch on the base» | RESOLVED | `supervisor.md:47` «(step 1)» |
| F6.3 [low] старое условие в `epic-files` и чертеже | RESOLVED в `epic-files` | `epic-files:150` «except once in `intake`, as its step 1 says (when and why)»; `BLUEPRINT.md:173` — см. «Не прослежено» |
| F7.2 [low] пауза до `plan_ok` снимается им | RESOLVED | `epic.yaml:141-142` «never / after `plan_ok`» |
| Все находки BACKLOG «epic 0.2.1» (18) | STILL OPEN, оставлены планом | BACKLOG |

## Вырезанные правила

Правка d86a6b4.

| Вырезанное правило (файл:строка в 785b306) | Где оно сейчас |
|---|---|
| `epic.yaml:28-29` «only while `git log origin/<base>..HEAD -- docs/epics/` shows no commit; never after» | **утеряно** как защита от сброса после коммита: reflog-проверка его не заменяет, когда сброс не двигал ветку — F12.5 (Passes: cut-rule check) |
| `epic.yaml:141-142` «only when its date is older than the gate … (a pause said at that gate or after it stays)» | `epic.yaml:140-142`, по ноте вместо дат; «keep a pause said at the gate itself» сохранено |
| `step-review:76` «`run` removes it only after a gate approved later than the pause» | ссылка на `run` шаг 3 |
| `epic-files:150` «before its first commit (`intake` says why)» | `epic-files:149-150` «as its step 1 says (when and why)» |

## Пропущено ранее

Нет.

## Оставлено планом

18 находок BACKLOG «epic 0.2.1», отложенных человеком на двух `release_ok`.

## Вопросы для человека

Нет: F12.5 — ошибка в самой правке, способ исправить ясен. Решение о релизе остаётся за человеком.

## Найдено по пути

- `[lado]` `flow_start` не принимает точку старта (`runs.py:90`). Именно поэтому киту приходится сбрасывать ветку и угадывать по git, был ли сброс. Параметр `base`/`from` у `flow_start` убрал бы весь этот механизм (F12.1–F12.5 этого отчёта).
