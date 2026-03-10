Выполни сценарий Day-Close для роли Стратег (R1).

> **Триггер:** Ручной — по запросу пользователя (`./scripts/strategist.sh day-close`).
> Отдельный файл отчёта НЕ создаётся. Итоги дня войдут в DayPlan следующего утра.

Источник сценария: /Users/user/Github/CLAUDE.md → Протокол Day-Close

## Контекст

- **WeekPlan:** /Users/user/Github/DS-my-strategy/current/WeekPlan W*.md (последний по дате)
- **MEMORY:** ~/.claude/projects/-Users-user-Github/memory/MEMORY.md
- **Exocortex backup:** /Users/user/Github/DS-my-strategy/exocortex/

## Алгоритм

### 1. Сбор коммитов за сегодня

```bash
# Для КАЖДОГО репо в /Users/user/Github/:
git -C /Users/user/Github/<repo> log --since="today 00:00" --oneline --no-merges
```

- Пройди по ВСЕМ репозиториям в `/Users/user/Github/`
- Сгруппируй коммиты по репозиториям
- Сопоставь с РП из недельного плана
- Определи статус каждого затронутого РП: done / partial / not started
- Выведи итоги на экран (не в файл)

### 2. Обновить WeekPlan

Найди текущий `WeekPlan W*.md` в `DS-my-strategy/current/` и обнови:

- Пометь завершённые РП как **done**
- Обнови статусы partial с описанием прогресса
- Добавь carry-over (что переносится на завтра) — в конец файла
- **НЕ удаляй** ничего — только помечай и дописывай

### 3. Обновить MEMORY.md

Синхронизируй статусы РП в MEMORY.md с обновлённым WeekPlan:
- done → done
- partial → in_progress (с пометкой прогресса)
- Удали завершённые РП из pending, если они в done

### 4. Backup экзокортекса

Скопируй актуальные файлы в `/Users/user/Github/DS-my-strategy/exocortex/`:

```bash
# Корневой CLAUDE.md
cp /Users/user/Github/CLAUDE.md /Users/user/Github/DS-my-strategy/exocortex/CLAUDE.md

# Memory (Слой 3)
cp ~/.claude/projects/-Users-user-Github/memory/MEMORY.md /Users/user/Github/DS-my-strategy/exocortex/MEMORY.md
cp ~/.claude/projects/-Users-user-Github/memory/*.md /Users/user/Github/DS-my-strategy/exocortex/
```

### 5. Закоммитить

- Закоммить все изменения в `DS-my-strategy` (WeekPlan + exocortex backup)
- Запуши

## Правила

- **Ничего не удалять** из WeekPlan — только помечать и дописывать
- **Не создавать отдельный файл отчёта** — итоги дня войдут в DayPlan следующего утра (шаг 1 day-plan)
- Если коммитов за день нет — написать «Нет активности» и всё равно сделать backup
- Выводить итоги на экран для пользователя

## Вывод на экран (шаблон)

```
📋 Day-Close: DD месяца YYYY

Коммиты: N в M репо
- repo-name: N коммитов (краткое описание)

РП обновлены в WeekPlan:
- #N: статус → новый статус

MEMORY.md: синхронизирован ✅
Exocortex backup: скопирован ✅
Git: закоммичен и запушен ✅
```

Результат: обновлённый WeekPlan + MEMORY.md + backup экзокортекса.
