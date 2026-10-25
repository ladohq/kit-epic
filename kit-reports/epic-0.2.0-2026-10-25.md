# Отчёт о ките: epic 0.2.0

- Дата: 2026-10-25
- Кит: корень worktree прогона `improve/epic-2` (`.lado/worktrees/kit-epic/improve-epic-2`), путь дан шагом
- Коммит: 160a6c9
- Оценил: критик kit-builder (слои a и b)
- Режим: повторная оценка, четвёртый визит `evaluate`. Предыдущий отчёт — эта же оценка на 3747819 (`approved`). Базой изменения служит 7e79334, её называет план. Правка этого визита: `git diff 3747819`, коммит 160a6c9. Это исправления F12.2 и F5.8 по отказу человека на `release_ok`; остальные находки ушли в BACKLOG «epic 0.2.1».
- Проходы:
  - три прохода по правке 160a6c9 и тексту вокруг неё: проход 1 сделал критик, проходы 2 (с flow, по сценариям) и 3 (с навыков) — свежие независимые субагенты;
  - полный проход по обоим затронутым файлам (`flows/epic.yaml`, `skills/step-review/SKILL.md`) сделал отдельный субагент. Остальные 8 файлов с полного прохода на 40e65d1 не менялись (`git diff 40e65d1 160a6c9` их не трогает, кроме BACKLOG). Полный проход не обязателен — вердикт `changes`, — но он уже шёл, и его находки подтверждены;
  - находки одного прохода: отброшено 0, оставлено как подтверждённые 4.

Находки — кандидаты, которые взвешивает человек. Это не оценка «сдал / не сдал».

## Карточка

| Слой | Результат |
|---|---|
| a. `lado kits check` | OK; 0 предупреждений |
| a. Бюджет | жёлтый: шагов в flow 7, гейтов 3; обе меры обоснованы в BLUEPRINT.md, раздел 4. Промпт лида 995 слов |
| a. Flows | нарисован 1; совпадает со скелетом чертежа |
| b. Рубрика | 7 новых находок в изменённом тексте (1 high, 3 medium, 3 low). Без находок 8 критериев из 12. F12.2 и F5.8 прошлого визита исправлены не до конца (см. ниже); остальные оставлены планом |
| Охват | повторная оценка правки 160a6c9 (2 файла), с полным проходом по этим 2 файлам |
| Правило остановки | не выполнено: 1 high, 3 medium |

Счёт относится только к тому, что названо в строке «Охват». Его нельзя сравнивать со счётом полной оценки.

**Вердикт: `changes`.** В новом условии сброса в `intake` осталась находка high (F12.3). Остальное блоком не является.

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

1. Условие сброса в `intake` проверять по собственной истории рана, а не по любым коммитам под `docs/epics/`, и в worktree рана (F12.3, F12.4).
2. Правило снятия `Pause asked` сделать одним и без сравнения дат. Например: `run` снимает паузу после `step_ok`, если нота `next`, которая привела к гейту, её называла; `step-review` только ссылается на `run` (F3.8, F5.9).

## Находки

Новые находки в правке 160a6c9. Номера продолжают отчёт на 3747819: его id остались в BACKLOG «epic 0.2.1».

### 1. Границы ролей

Без находок. Сброс и правка State по-прежнему за лидом, в worktree рана.

### 2. Передача между шагами

Без находок. Пауза живёт в State, нота `next` её называет.

### 3. Готовность и исходы

- **F3.8** [medium] `flows/epic.yaml:141` (state `run`)
  >          only when its date is older than the gate the human just approved (a pause said
  В строке `Pause asked: <date>` только дата (`skills/epic-files/SKILL.md:47`), а время гейта лид знает разве что как «сейчас». Пример: пауза утром, `review` в тот же день даёт `next`, человек одобряет `step_ok` днём. Дата паузы не «older», строка остаётся, и каждый следующий `review` снова открывает `step_ok`, которого человек не просил. Лид, читающий «older» вольно, наоборот снимет паузу, сказанную на самом гейте. От прогона к прогону по-разному.
  Fix: решать по гейту, а не по датам: снимать `Pause asked` после `step_ok`, если нота `next`, приведшая к гейту, называла паузу; или писать `Pause asked: <date time>`. Passes: 3/3 + full pass

### 4. Независимая проверка

Без находок. Гейты не менялись.

### 5. Противоречия

- **F5.9** [medium] `skills/step-review/SKILL.md:76`
  > after a lost context; `run` removes it only after a gate approved later than the pause.
  Две копии правила уже расходятся. Здесь паузу снимают после гейта, одобренного позже паузы. Значит, пауза, сказанная, пока гейт открыт, снимается его одобрением. Во flow (`epic.yaml:141-142`) — наоборот: «(a pause said / at that gate or after it stays)». Лид следует той копии, которую прочитал.
  Fix: одно правило в `run` шаг 3; в `step-review` — только ссылка «`run` removes it as its step 3 says». Passes: 2/3 (проход 2 и полный проход)

- **F5.10** [low] `agents/supervisor.md:47`
  > the settings' chat language. `intake` puts its branch on the base. Several epics may go
  Сброс теперь условный, а роль лида по-прежнему говорит, что он есть всегда. Когда `intake` его пропускает, лид уверен, что ветка на базе.
  Fix: «`intake` puts its branch on the base before its first commit (`intake` says when)». Passes: 1/3, confirmed

### 6. Дублирование

- **F6.3** [low] `skills/epic-files/SKILL.md:150`
  >   branch, except once in `intake` before its first commit (`intake` says why): later, a
  Здесь и в `BLUEPRINT.md:173` («except once at `intake`, before its first commit») осталось старое условие, которое лид после потери контекста проверить не может. Проверка через `git log` есть только в `intake`. Строки в журнале чертежа (§5) для правки 160a6c9 нет.
  Fix: в обоих местах — «while `intake`'s check allows it (`intake`)»; строка §5. Passes: 1/3, confirmed

### 7. Когда звать человека

- **F7.2** [low] `flows/epic.yaml:141` (state `run`)
  >          only when its date is older than the gate the human just approved (a pause said
  Пауза, сказанная в `intake`, `plan` или `plan_review`, старше `plan_ok`, и `run` после `plan_ok` её снимет: ни одно ревью из-за неё не остановится, вопреки README «stop at the next review». Человеку об этом не скажут.
  Fix: снимать `Pause asked` только после `step_ok` — единственного гейта, к которому ведёт пауза. Passes: 1/3, confirmed

### 8. Петли на повторном визите

Без находок. Петля и её предел не менялись. Лишние раунды `step_ok` из F3.8 всё равно идут через гейт.

### 9. Краткость и «почему»

Без находок. У обеих правок причина записана рядом: «or a lost context would erase the human's committed answers».

### 10. Описания навыков

Без находок.

### 11. Нейтральность к провайдеру

Без находок. Только `git`.

### 12. Безопасность и рамки

- **F12.3** [high] `flows/epic.yaml:28-29` (state `intake`)
  >          origin/<base>` in the run's worktree, the one reset of the run's branch, and only
  >          while `git log origin/<base>..HEAD -- docs/epics/` shows no commit; never after,
  Проверка не отличает коммиты этого рана от коммитов человека. LADO начинает ветку рана от HEAD человека. Если этот HEAD не на базе и в нём есть коммит под `docs/epics/`, которого нет на `origin/<base>`, — например, бриф, набросанный вручную, правки плана продолжаемого эпика или ветка прежнего рана, — `git log` покажет коммит при самом первом визите. Сброс запрещён, и все прочие коммиты человека остаются в ветке. После `plan_ok` PR плана после зелёного CI сольёт их в базу, хотя `plan_ok` их не показывал. Это ровно тот мерж в базу, ради которого сброс добавлен (F12.1, «Пропущено ранее» на fd61f24).
  Уровни у проходов разошлись: проход 2 — high, проход 3 — medium, проход 1 и полный проход — low (случай редкий). По правилу рубрики взят высший, так что находка блокирует. Без такого коммита проверка работает верно.
  Fix: связать условие с историей самого рана, например «only while `git reflog show <the run's branch>` has no entry after its creation», или помечать коммиты `intake` именем рана и проверять `git log origin/<base>..HEAD --grep=<run>`; при чужих коммитах под `docs/epics/` до первого коммита рана — спросить человека. Passes: 3/3 + full pass (уровни high / medium / low; взят high)

- **F12.4** [medium] `flows/epic.yaml:29` (state `intake`)
  >          while `git log origin/<base>..HEAD -- docs/epics/` shows no commit; never after,
  «In the run's worktree» в тексте относится к сбросу, но не к проверке. Шаги лида LADO выполняет в его репозитории, т. е. в checkout человека (`lado` `runs.py:9-10`: «Supervisor steps are / done in the session's repo»). После потери контекста `git log` в checkout человека не покажет коммитов эпика, и сброс в worktree сотрёт уже закоммиченные ответы — то, что правка должна была предотвратить.
  Fix: «while `git -C <the run's worktree> log origin/<base>..HEAD -- docs/epics/` shows no commit». Passes: full pass, confirmed

## Известные дыры

| Известная дыра | Находка или как кит её закрывает |
|---|---|
| 1. Красная проверка без учёта окружения | Закрыта (без изменений): `epic-files:160` «First tell whether the work caused it» |
| 2. Работа вне flow, мерж без гейта | Открыто: F12.3 — при чужих коммитах под `docs/epics/` в HEAD человека PR плана сольёт в базу коммиты, которых гейт не показывал |
| 3. Путь вне worktree рана | Открыто: F12.4 — проверка сброса может пройти в checkout человека |
| 4. Вердикт без порога | Закрыта (без изменений) |
| 5. Навык зависимости пишет или спрашивает, где роли нельзя | Закрыта (без изменений) |
| 6. Команды не в ладу с `expects.commands` | Закрыта: только `git` |
| 7. Read-only роли не в ладу с работой | Закрыта (без изменений) |

## Не прослежено

Ничего: новых элементов нет, жёлтые меры обоснованы, `--compare` совпадает. Отставание чертежа — F6.3 (и F5.6 в BACKLOG).

## Прежние находки

Отчёт на 3747819.

| Находка | Статус | Чем подтверждено |
|---|---|---|
| F12.2 [medium] сброс в `intake` после потери контекста | RESOLVED для своего случая, с новой дырой | `epic.yaml:29` «while `git log origin/<base>..HEAD -- docs/epics/` shows no commit; never after»; проверка не отличает чужие коммиты (F12.3) и может пройти не в том дереве (F12.4) |
| F5.8 [medium] пауза стирается тем же одобрением | RESOLVED в намерении, с новой дырой | `epic.yaml:141-142` «(a pause said / at that gate or after it stays)»; условие по датам невыполнимо (F3.8), копия в `step-review` расходится (F5.9) |
| F5.7, F3.5, F3.6, F3.7, F6.2, F9.2, F2.3, F8.2 | STILL OPEN, оставлены планом | BACKLOG «epic 0.2.1», вторая часть («Left for later at the second `release_ok`») |
| F2.1, F3.3, F2.2, F3.1, F3.2, F3.4, F5.5, F5.6, F8.1, F9.1 | STILL OPEN, оставлены планом | BACKLOG «epic 0.2.1», первая часть |

## Вырезанные правила

Правка 160a6c9.

| Вырезанное правило (файл:строка в 3747819) | Где оно сейчас |
|---|---|
| `epic.yaml:138-139` «remove a pause from the State (the line `Pause asked`, or Phase `paused (waits for sdlc)`)» | `epic.yaml:140-142`: Phase снимается всегда, `Pause asked` — с условием |
| `step-review:76` «`run` removes it when the human approves the next step» | `step-review:76` «only after a gate approved later than the pause» (расходится с flow — F5.9) |

Утерянных правил нет.

## Пропущено ранее

Нет новых.

## Оставлено планом

Все находки из BACKLOG «epic 0.2.1» (18 штук), отложенные человеком на двух `release_ok`; список — в таблице «Прежние находки».

## Вопросы для человека

1. F12.3 блокирует по правилу «берётся высший уровень», хотя случай редкий: HEAD человека не на базе и уже содержит коммит под `docs/epics/`. Исправить (одно условие в `intake`) или принять риск и отложить? Рекомендация: исправить вместе с F12.4 — это одно и то же предложение, и правка небольшая.

## Найдено по пути

- `[lado]` `flow_start` не принимает точку старта: ран начинается от HEAD репозитория лида (`runs.py:90`). Из-за этого кит вынужден сбрасывать ветку в `intake` и угадывать, какие коммиты в ней свои (F12.3). Параметр `base`/`from` у `flow_start` снял бы проблему целиком.
