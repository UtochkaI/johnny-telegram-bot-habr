# johnny_article_outline_v1

Дата сборки: 2026-05-28  
Источник: `johnny_article_codex_pack_v2.zip` + подтверждение автора по origin story.

## 0. Рабочая формулировка статьи

**Вариант заголовка:**  
Как плохой VPS для VPN случайно превратился в злого Telegram-бота-инженера

**Подзаголовок:**  
История о том, почему Telegram LLM-бот — это не только prompt engineering, а routing, suppress, side-effect boundary, regression matrix, systemd, backups и много live-QA.

**Короткий тезис статьи:**  
Проект начался как попытка использовать уже оплаченный VPS, который не подошёл под VPN. В итоге из простого эксперимента вырос Telegram-бот с характером, памятью, реакциями, image/vision, production-ограничениями и отдельной дисциплиной регрессионных проверок.

**Тон:**  
Инженерный разбор с живой завязкой. Не рекламный README и не сухой changelog. Основной интерес — как бытовая проблема с VPS перешла в проектирование поведения LLM в живом групповом чате.

## 1. Что уже подтверждено, а что требует осторожности

### Подтверждено пользователем

- Проект начался из-за VPS у `62Yun` (`https://62yun.ru/`), который не подошёл под VPN.
- Проблемы по пользовательскому опыту: падения, постоянный DDoS, технические работы, игнорирование со стороны техподдержки, сайт провайдера не всегда работает.
- Сервер был арендован на 1 месяц.
- На балансе оставалось около 1200 рублей, которые нельзя было вывести.
- Первый запрос к ChatGPT был по смыслу: “придумай что-нибудь не банальное и забавное”.
- Сначала это был просто эксперимент, не готовая идея Telegram-бота и не сразу персонаж Джонни.

### Подтверждено summary/source pack

- Source pack покрывает 40 summary-секций: ранние файлы 2026-05-04 → `v02_34`.
- Работа велась как серверный проект с бэкапами перед патчами.
- Важные направления: cooldown queue, context/target separation, routing/suppress, memory participant guard, fail-soft model/API behavior, semantic live stages H0-H4, reactions, image generation, vision, side-effect boundary, DB/history sanitize, regression matrix.
- В summary есть известный mismatch: папка `19 v02_10`, файл `johnny_bot_work_summary_v02_09.md`.

### Осторожные формулировки

- Про проблемы 62Yun писать как личный опыт автора, не как независимый аудит.
- Не утверждать, что каждый патч-маркер из summary точно остался активен в финальном production-коде.
- Не вставлять chat_id, sender_id, IP, токены, приватные фрагменты логов.
- Если нужны технические доказательства в статье, брать только маркеры/обобщения, а не сырые логи.

## 2. Сюжетная дуга

1. **Плохой VPS как триггер.**  
   VPN-сервер не получился надёжным. Деньги на балансе вывести нельзя. Возникает вопрос: что сделать с уже оплаченным ресурсом?

2. **Эксперимент вместо полезного сервиса.**  
   Запрос к ChatGPT: “придумай что-нибудь не банальное и забавное”. Сначала это не продукт, а проба: можно ли поднять что-то живое на сервере.

3. **Появляется Telegram LLM-бот.**  
   Базовая архитектура: Telegram, LLM API, сервер, файлы проекта, память/контекст, systemd и бэкапы.

4. **Появляется Джонни как персонаж.**  
   Не “вежливый ассистент”, а злой инженер, который ненавидит халтуру. Персонаж становится UX-слоем.

5. **Главная проблема оказывается не в генерации ответов.**  
   Бот может отвечать, но в групповом чате важнее другое: когда молчать, когда отвечать, кому отвечать, как не перехватить чужой контекст.

6. **Routing/suppress становится ядром проекта.**  
   Появляются reply-only, trigger/mention rules, cooldown, queue, bot-share, anti-chain, low-value suppress, stale checks.

7. **Бот становится “слишком активным”.**  
   Жалобы админов превращаются в product requirement: Джонни должен быть живым, но не должен захватывать чат.

8. **Появляются реакции, image generation и vision.**  
   Каждая новая функция оказывается side-effect: публичная отправка текста, файла, реакции, картинки или записи в БД.

9. **Регрессии учат процессу.**  
   `py_compile` ловит синтаксис, но не ловит поведение. CLI-smoke не доказывает Telegram side-effect. Нужны dry-run, fake client, live-smoke, log check, DB/history leak check.

10. **Итоговый вывод.**  
    LLM-бот в живом Telegram-чате — это не “подключить модель и написать prompt”. Это runtime policy: routing, suppress, context boundaries, side-effect guards, regression discipline и эксплуатация.

## 3. Рекомендуемая структура статьи

### 3.1. Вступление: “Я хотел VPN, а получил Джонни”

**Цель раздела:** зацепить читателя бытовой инженерной ситуацией.

**Материал:**
- 62Yun, VPN оказался непригоден по личному опыту.
- Деньги вывести нельзя, на балансе около 1200 ₽.
- Запрос: придумать что-то небанальное и забавное.
- Сначала просто эксперимент.

**Черновая формулировка:**
> Я арендовал VPS под VPN, но вместо спокойного сервера получил ежедневный аттракцион: падения, DDoS, техработы, молчащую поддержку и сайт провайдера, который сам периодически лежит. Деньги вывести нельзя, на балансе оставалось около 1200 ₽. Поэтому я задал ChatGPT не вопрос “как поднять очередной сервис”, а что-то ближе к: “придумай что-нибудь не банальное и забавное”.

**Источник:** `johnny_article_origin_note.md`.

**Осторожность:** писать “по моему опыту”, “в моём случае”, “для VPN-задачи не подошёл”.

### 3.2. Первый прототип: из оплаченного сервера в Telegram LLM-эксперимент

**Цель раздела:** показать, что проект не стартовал как зрелый продукт.

**Тезисы:**
- Был сервер.
- Был Telegram как удобный интерфейс.
- Была LLM-модель.
- В начале задача была “поиграться”, а не строить production.

**Технические элементы:**
- server workdir;
- local/server backups;
- early bot files;
- URL/current-date context;
- queue cooldown.

**Источник:** `johnny_article_timeline.md`, ранние строки source 02–03.

### 3.3. Рождение Джонни: character layer как UX

**Цель раздела:** объяснить, почему бот стал интересным, а не просто полезным.

**Тезисы:**
- Джонни — не саппорт и не “полезный ассистент”.
- Его образ: production-инженер, раздражённый халтурой.
- Character layer делает бота запоминающимся, но создаёт риск: стиль не должен ломать routing, truthfulness и safety.

**Технические опоры:**
- `PATCHED_SURFACE_STYLE`
- `PATCHED_BARBED_TECH_TUNING`
- `PATCHED_STAGE4_MOOD_TOXICITY`
- `PATCHED_STAGE4G3_CREDIT_AND_METRIC_TRUTHFULNESS`
- `PATCHED_STAGE4X_ANSWER_SHAPE_VARIATION`

**Источник:** `johnny_article_patch_index.md`, `johnny_article_timeline.md`.

### 3.4. Первая серьёзная проблема: cooldown не должен уничтожать задачу

**Цель раздела:** показать ранний пример, где “бот просто отвечает” недостаточно.

**Тезисы:**
- Addressed message during cooldown мог быть потерян.
- Потерянная задача могла потом загрязнить другой ответ.
- Решение: cooldown delays sending, not destroys the task.

**Маркеры:**
- `PATCHED_QUEUE_COOLDOWN`

**Источник:** `johnny_article_key_events.md`, `johnny_article_patch_index.md`.

**Инженерный вывод:** очередь и отложенная отправка — это не UX-мелочь, а защита от context leak.

### 3.5. TARGET_MESSAGE против RECENT_CONTEXT

**Цель раздела:** объяснить одну из главных архитектурных границ.

**Тезисы:**
- `TARGET_MESSAGE` — задача пользователя.
- `RECENT_CONTEXT` — фон.
- Бот не должен отвечать на фон как на новый запрос.
- Эта проблема будет возвращаться в context/reference/memory/stale branches.

**Маркеры:**
- `PATCHED_STAGE3_TARGET_CONTEXT_HARDENING`
- `PATCHED_URL_FETCHER`
- `PATCHED_STAGEG_SEMANTIC_ATMOSPHERE_HINT`

**Источник:** `johnny_article_conflicts.md`, `johnny_article_patch_index.md`.

### 3.6. Routing/suppress: как научить бота молчать

**Цель раздела:** центральная техническая глава.

**Тезисы:**
- В групповом чате ценность бота определяется не только качеством ответа, но и умением не вмешиваться.
- Сигналы: direct reply, trigger, mention, ignore_reply, forced addressed reply.
- Suppress нужен не как “затычка”, а как runtime policy.

**Ключевые конфликты:**
- direct-addressed messages обычно надо отвечать;
- low-value/noise надо suppress;
- bot-share clamps защищают чат от дуэлей;
- short underspecified prompts требуют clarification, не invented context.

**Маркеры:**
- `PATCHED_SILENT_FIX`
- `PATCHED_ANTI_DUEL_GUARD_LINEPATCH`
- `PATCHED_STAGEG_BOT_DUEL_TAKEDOWN_GUARD`
- `PATCHED_STAGEG_STOP_BOT_DUEL_MICROFIX`
- `PATCHED_STAGEH0_SKIP_OLD_ADDRESSED_MESSAGES`
- `PATCHED_STAGE1_*`
- `PATCHED_STAGE_CONTEXT_UNDERSPECIFIED_*`

**Источник:** `johnny_article_conflicts.md`, `johnny_article_patch_index.md`.

### 3.7. Когда Джонни стало слишком много

**Цель раздела:** продуктовый поворот.

**Тезисы:**
- Админы/участники стали воспринимать бота как слишком активного.
- Это изменило цель: не “пусть отвечает умно”, а “пусть не захватывает чат”.
- Появляются bot-share, cooldown, reply-chain guards, low-value suppress, reactions.

**Маркеры/темы:**
- `bot_share`
- `cooldown`
- `low_value`
- `anti-chain`
- `reply-chain`
- `stage1_suppressed`

**Источник:** `johnny_article_conflicts.md`, `article_ultra_digest_for_codex.md`.

**Что желательно добавить:** 1–2 обезличенных фрагмента живого чата, где видно “Джонни слишком много”.

### 3.8. Semantic live H0-H4: когда бот начинает сам вмешиваться

**Цель раздела:** показать сложность proactive behavior.

**Тезисы:**
- Semantic live делает бота живее, но резко повышает риск спама.
- Поэтому stages шли через dry-run, source binding, seen-source guards, duplicate guards, write-forbidden guards.
- Reply-bound live message должен отвечать source message, а не просто писать рядом.

**Маркеры:**
- `PATCHED_STAGEH0_SEMANTIC_INTERVENTION_DRYRUN`
- `PATCHED_STAGEH0_FINAL_SOURCE_GATE`
- `PATCHED_STAGEH0_LIVE_SENDER`
- `PATCHED_STAGEH0_REPLY_TO_SOURCE_MESSAGE`
- `PATCHED_STAGEH0_REQUIRE_REPLYABLE_SOURCES`
- `PATCHED_STAGEH0_SEEN_SOURCE_SKIP`
- `PATCHED_STAGEH0_PERSISTENT_REPLY_DUP_GUARD`
- `PATCHED_STAGEH1*`
- `PATCHED_STAGEH2*`
- `PATCHED_STAGEH3*`
- `PATCHED_STAGEH4*`

**Источник:** `johnny_article_timeline.md`, `johnny_article_patch_index.md`.

### 3.9. Reactions: маленький side-effect, большая дисциплина

**Цель раздела:** показать, почему даже реакция — это production side-effect.

**Тезисы:**
- Reactions выглядели как хороший способ заменить лишний текст.
- Первая реализация ушла в lexical trigger lists и была откатана.
- Был syntax break / conceptual rollback.
- Вывод: reaction должен быть semantic, high-confidence, no default OK, public-guarded.

**Маркеры:**
- `PATCHED_V0211_SEMANTIC_PRE_REACTION_GATE`
- `PATCHED_V0211_SEMANTIC_FALLBACK_REACTIONS`
- `PATCHED_V0211_REACTION_INVALID_FALLBACK_CHAIN`
- `PATCHED_P0J_REACTION_*`

**Источник:** `johnny_article_conflicts.md`, `johnny_article_patch_index.md`.

### 3.10. Image/Vision: генерация картинок как side-effect boundary

**Цель раздела:** отдельная сильная техническая глава.

**Тезисы:**
- Image generation — не “ещё один ответ модели”, а отдельная side-effect ветка.
- Нужны enabled gates, cooldown, dry-run, public-send guards.
- Vision context должен помогать по текущему изображению, но не тянуть stale image memory.
- Debug/service markers не должны попадать в history.

**Маркеры:**
- `PATCHED_STAGE9_*`
- `PATCHED_STAGE9_PUBLIC_RUNTIME_LEAK_GUARD`
- `PATCHED_STAGE9_IMAGE_REQUEST_BYPASS_IGNORE`
- `PATCHED_STAGE_VISION*`
- `PATCHED_STAGE_MEMORY_RETRIEVAL_*`
- `PATCHED_P0I*`

**Источник:** `johnny_article_timeline.md`, `johnny_article_conflicts.md`, `johnny_article_patch_index.md`.

**Что можно показать:** пример funny image generation с Джонни, но без приватных данных.

### 3.11. DB/history sanitize: когда служебное не должно стать частью личности

**Цель раздела:** объяснить persistence risk.

**Тезисы:**
- В чат-боте история — не просто лог, а будущий контекст модели.
- Если туда попадают runtime markers/debug/service status, бот начинает есть собственную телеметрию.
- Нужны guards: public runtime leak guard, history sanitize, DB event accounting, idempotency.

**Темы/маркеры:**
- `marker leak`
- `DB-history sanitize`
- `PATCHED_STAGE9_PUBLIC_RUNTIME_LEAK_GUARD`
- `PATCHED_P0G/P0H`
- WAL / busy-timeout / safe history writes

**Источник:** `johnny_article_key_events.md`, `johnny_article_conflicts.md`.

### 3.12. Почему `py_compile` не спасает

**Цель раздела:** кульминационный инженерный вывод.

**Тезисы:**
- `py_compile` доказывает только синтаксис.
- CLI-smoke не доказывает Telegram delivery, queue, cooldown, reply-to, random ignores, delays.
- Runtime branch может упасть там, куда CLI не дошёл.
- В проекте были случаи: syntax break, runtime NameError, live behavior regressions.

**Что описать:**
- Reaction rollback.
- Runtime NameError.
- Hard clamp / suppress live UX.
- Пользователь стал live-QA.

**Источник:** `johnny_article_conflicts.md`, `article_ultra_digest_for_codex.md`, предыдущий QA-фрагмент из чата.

### 3.13. Regression matrix и layer discipline

**Цель раздела:** дать практический takeaway.

**Тезисы:**
- Патч нельзя закрывать по “маркер есть + py_compile ok”.
- Для routing/suppress/context/reference/style нужна CLI matrix.
- Для side-effect/send/file/reaction/image нужна dry-run/fake-client/live-smoke/log check.
- Проблему надо лечить в её слое: routing, suppress, memory, context, prompt, reference, style, side-effect, image, reaction, DB, CLI, service.

**Практический список для статьи:**
```text
routing/suppress/context/reference/style:
  py_compile
  CLI matrix
  targeted scenario checks

side-effect/send/file/reaction/image:
  py_compile
  fake-client / dry-run
  controlled live-smoke
  fresh bot.log ERROR/Traceback check
  DB/history marker leak check
```

**Источник:** `johnny_article_conflicts.md`, project rules.

### 3.14. Итог: что на самом деле строилось

**Цель раздела:** закрыть статью сильным выводом.

**Финальный тезис:**
- Снаружи это Telegram-бот с резким характером.
- Внутри это runtime-policy engine вокруг LLM.
- Главные сущности: target message, recent context, participant memory, routing gates, suppress policy, side-effect boundary, regression discipline.

**Финальная формулировка:**
> Я начинал с вопроса, куда деть раздражающий VPS. А закончил системой, где самая сложная часть — не сгенерировать остроумный ответ, а доказать, что бот имеет право вообще что-то сказать.

## 4. Что вставить в статью как технические блоки

### Блок 1. Мини-архитектура

```text
Telegram update
  -> message normalization
  -> routing/addressing
  -> suppress gates
  -> context/reference/memory builder
  -> LLM call
  -> postprocess/style
  -> side-effect boundary
  -> Telegram send / reaction / image / DB write
```

### Блок 2. Layer discipline

```text
routing / suppress / memory / context / prompt / reference / style /
side-effect / image / reaction / DB / CLI / service
```

### Блок 3. Regression checklist

```text
- addressed high-priority question
- reply follow-up
- low-value reply-to-bot -> reaction or suppress by rule
- naked trigger -> suppress
- image request route
- dry-run no public send
- DB/history marker leak check
- fresh log check for ERROR/Traceback
```

### Блок 4. Side-effect boundary

```text
Text send, file send, reaction, image generation, memory write and live semantic send
are not "just model output". They are production side effects.
```

## 5. Иллюстрации / скриншоты

Желательно подготовить:

1. Скрин/схема “плохой VPS → эксперимент → Джонни”.
2. Мини-диаграмма routing/suppress.
3. Скрин или обезличенный фрагмент чата, где Джонни проявляет характер.
4. Пример “слишком много Джонни” без приватных данных.
5. Пример image generation с персонажем.
6. Таблица “py_compile / CLI / dry-run / live-smoke — что каждый слой доказывает и не доказывает”.

## 6. Что не стоит вставлять

- Полный patch index на 162 строки.
- Сырые логи.
- IP, токены, chat_id, sender_id.
- Длинный changelog всех `PATCHED_*`.
- Все версии подряд без отбора.
- Юридически жёсткие утверждения про провайдера без формулировки “мой опыт”.

## 7. Что нужно запросить/подготовить перед черновиком

1. 3–5 живых обезличенных диалогов с Джонни.
2. 1–2 случая, где бот был реально полезен.
3. 1–2 случая, где бот был слишком активным.
4. 1 случай regression/bug, который хорошо объясняет процесс.
5. 1 пример image/vision.
6. Решение по публикации: Habr, GitHub README, Telegram post или серия статей.

## 8. Рекомендуемый формат публикации

### Вариант A — одна Habr-статья

Фокус: история + инженерные выводы.  
Минус: придётся сильно сжать технические детали.

### Вариант B — серия из 2 статей

1. “Как плохой VPS стал Telegram LLM-ботом Джонни”.
2. “Routing/suppress/side-effect boundary: почему LLM-бот в группе — это production”.

### Вариант C — GitHub + Habr

- GitHub: sanitized README, архитектура, конфиги без секретов, тестовые сценарии.
- Habr: история, разбор решений, выводы.

Рекомендация: **B или C**. Материала слишком много для одной чистой статьи, если не превращать её в простыню.

## 9. Следующий шаг

Собрать `johnny_article_draft_v1.md` по этой структуре.  
Перед этим желательно выбрать формат:

- **короткая статья 8–12 тыс. знаков** — история + выводы;
- **средняя 18–25 тыс. знаков** — история + основные технические блоки;
- **длинная 35–50 тыс. знаков** — полноценный инженерный разбор.
