# Audit — UML Component Diagram

## 1. Обсяг і джерела перевірки

Перевірено відповідність `component_diagram.md` та `spec.md` попереднім
моделям:

- **Дані:** `lab1/spec.md`, `lab1/diagram.md`.
- **Вимоги та use cases:** `lab2/requirements.md`, `lab2/traceability.md`.
- **Взаємодія:** `lab3/sequence/SC-07.puml`,
  `lab3/sequence/SC-09.puml`.
- **Процес:** `lab4/spec.md`, `lab4/activity.md`.

## 2. Межа системи

| Область | Компоненти |
|---|---|
| Усередині `Online Judge Platform` | Web UI, API / Backend, Authentication Service, Problem Service, Submission Service, Judge Service, Editorial Service, Premium Service, Data Access Layer, Application Database |
| Зовні системи | Payment Service, OAuth / Identity Provider, Email/SMS Service, Code Runner / Sandbox |

Межа в діаграмі задана `package "Online Judge Platform"`. Усі компоненти
з переліку `Inside the system` розміщені всередині пакета, а зовнішні
інтеграції — поза ним. `IPaymentGateway` також розміщений за межею як
контракт, який надає зовнішній Payment Service і використовує Premium Service.

## 3. Provided / required interfaces

| Контракт | Provider | Consumer | Перевірка |
|---|---|---|---|
| `IWebApi` | API / Backend | Web UI | Відповідає `spec.md`; у діаграмі `WebUI ..> IWebApi`, `API - IWebApi` |
| `IAuthentication` | Authentication Service | API / Backend | Відповідає `REQ-04`–`REQ-07`; використання показано через API |
| `IProblemManagement` | Problem Service | API / Backend | Відповідає `REQ-01`–`REQ-03`; використання показано через API |
| `IJudge` | Judge Service | Submission Service | Відповідає `REQ-09`–`REQ-14`; узгоджується з `SC-07` і `SC-09` |
| `IPaymentGateway` | Payment Service | Premium Service | Відповідає `REQ-20`–`REQ-23`, `REQ-26`, `REQ-27`; контракт перетинає системну межу |

У `spec.md` provided і required інтерфейси описані окремими таблицями, а в
діаграмі для кожного контракту показані provider та consumer.

## 4. Трасування компонентів

| Компонент | Дані | Вимоги / use cases | Взаємодія | Процес | Статус |
|---|---|---|---|---|---|
| Web UI | `User`, `Problem`, `Submission`, `EditorialPost` у `lab1/spec.md` | `REQ-01`–`REQ-03`, `REQ-08`, `REQ-12`, `REQ-13`, `REQ-16`, `REQ-24`, `REQ-25`; `UC-01`, `UC-05`, `UC-07`, `UC-10` | `SC-07`, `SC-09`: форма надсилання та показ результату | `lab4/spec.md`: показ форми, черги та вердикту | Покрито |
| API / Backend | Операції над усіма сутностями через DAL | Усі use cases як API-координатор; особливо `REQ-04`–`REQ-07`, `REQ-09`, `REQ-18`, `REQ-20` | `SubmissionController` у `SC-07`, `SC-09` | Валідація запиту та запуск `createPendingSubmission` у `lab4` | Покрито на рівні компонента |
| Authentication Service | `User` | `REQ-04`–`REQ-07`; `UC-03`, `UC-04` | Контракт `IAuthentication` | Передумова авторизованого користувача в `lab4/spec.md` | Покрито частково |
| Problem Service | `Problem`, `Tag`, `TestCase` | `REQ-01`–`REQ-03`, `REQ-28`, `REQ-29`; `UC-01`, `UC-02`, `UC-11` | Контракт `IProblemManagement` | API перевіряє наявність і доступність задачі | Покрито |
| Submission Service | `Submission`, `Language`, `EditorialPost` | `REQ-08`–`REQ-19`; `UC-05`–`UC-08`, `UC-13` | `IJudge`, а також виклики `markAsAccepted` / помилкових статусів | Створення `Pending`, зміна статусу, збереження метрик та історії | Покрито |
| Judge Service | `TestCase`, `Submission` | `REQ-10`–`REQ-14`; `UC-06`, `UC-13` | `SC-07`, `SC-09`: завантаження тестів, виконання, перевірка результату | Цикл тестів і розгалуження Accepted / Wrong Answer / TLE | Покрито |
| Editorial Service | `EditorialPost`, `User`, `Submission` | `REQ-18`, `REQ-19`; `UC-08` | Взаємодія через API, прив’язка до прийнятого submission | Публікація editorial після перевірки дозволів | Покрито на рівні компонента |
| Premium Service | `User.has_premium` | `REQ-20`–`REQ-27`; `UC-09`, `UC-10`, `UC-12` | `IPaymentGateway` | Оформлення, відмова, повторне списання та подарунок Premium | Покрито |
| Data Access Layer | Усі сутності `lab1` | Зберігання стану, потрібного для `REQ-01`–`REQ-29` | DB-виклики у sequence-діаграмах | Читання тестів, створення submission, збереження результатів | Покрито на архітектурному рівні |
| Application Database | ER-модель `lab1`: `User`, `Problem`, `Tag`, `Submission`, `Language`, `TestCase`, `EditorialPost` | Поля та стани, використані вимогами | `DB` у `SC-07` і `SC-09` | Зберігає submission, статуси, метрики, історію та тест-кейси | Частково: є розриви схеми, наведені нижче |

## 5. Трасування залежностей

| Залежність у діаграмі | Обґрунтування в попередніх моделях | Статус |
|---|---|---|
| Web UI → `IWebApi` → API / Backend | Користувач працює через UI; `SC-07`, `SC-09` показують UI → Controller | Покрито |
| API / Backend → `IAuthentication` → Authentication Service | `REQ-04`–`REQ-07`, `UC-03`, `UC-04` | Покрито |
| API / Backend → `IProblemManagement` → Problem Service | `REQ-01`–`REQ-03`, `UC-01`, `UC-02`, `UC-11` | Покрито |
| Submission Service → `IJudge` → Judge Service | `REQ-09`–`REQ-14`, `SC-07`, `SC-09`, `lab4/activity.md` | Покрито |
| Premium Service → `IPaymentGateway` → Payment Service | `REQ-20`–`REQ-23`, `REQ-26`, `REQ-27`, `UC-09`, `UC-12` | Покрито |
| API / Backend → Submission Service | `UC-05`; `createPendingSubmission` у `lab4/spec.md` | Покрито |
| API / Backend → Editorial Service | `REQ-18`, `REQ-19`, `UC-08` | Покрито на рівні use case |
| API / Backend → Premium Service | `UC-09`, `UC-10`, `UC-12` | Покрито на рівні use case |
| Authentication Service → Data Access Layer | Дані `User` у `lab1`; вимоги реєстрації та входу | Покрито на рівні архітектури |
| Problem / Submission / Judge / Editorial / Premium Services → Data Access Layer | Сутності та зв’язки `lab1`; persistence-кроки у `lab4` | Покрито |
| Data Access Layer → Application Database | DB-виклики в `SC-07`, `SC-09` і `lab4/activity.md` | Покрито |
| Authentication Service → OAuth / Identity Provider | Зовнішня інтеграція з `spec.md`; попередні моделі не деталізують протокол | Частково |
| Authentication Service → Email/SMS Service | Зовнішня інтеграція з `spec.md` | Не трасується до попередніх вимог |
| Judge Service → Code Runner / Sandbox | `REQ-14`, `SC-07`, `SC-09`, `lab4/spec.md` | Покрито |

## 6. Топ-3 розбіжності та рішення

1. **Автентифікаційні поля даних.** `lab1/spec.md` містить поле `User.password`,
   а `lab2/requirements.md` вимагає зберігати пароль у `password_hash`
   (`REQ-05`) і використовує `role` в `REQ-29`. `role` та
   `password_hash` відсутні в ER-моделі. Це розрив між моделлю даних і
   вимогами; його потрібно виправити в моделі даних перед реалізацією.

2. **Дані складності рішення.** `REQ-15`–`REQ-17` і `lab4/spec.md`
   вимагають `time_complexity` та `space_complexity` у `Submission`, але
   ці поля відсутні в `lab1/spec.md` і `lab1/diagram.md`. Компонентна
   модель правильно відносить збереження до Submission Service / DAL, але
   схема бази даних потребує доповнення.

3. **Email/SMS не має попередньої трасованості.** Email/SMS Service
   присутній як зовнішня інтеграція в `lab5/spec.md` і діаграмі, однак
   окремої вимоги, use case, sequence або process-кроку для надсилання
   повідомлень у `lab1`–`lab4` немає. Інтеграцію слід або підтвердити
   вимогами та сценаріями, або вилучити з поточної компонентної моделі.

## 7. Підсумок аудиту

- Межа системи чітка та відповідає перелікам inside/outside.
- Усі provided/required-інтерфейси з `spec.md` мають provider і consumer.
- Основний ланцюг `Web UI → API → Submission → Judge → Code Runner → DB`
  підтверджений вимогами, sequence-діаграмами та activity/process-моделлю.
- Компонентна модель узгоджена з попередніми артефактами на архітектурному
  рівні, але три розриви, наведені вище, потрібно врахувати перед
  реалізацією або явно закрити в наступній ітерації моделей.