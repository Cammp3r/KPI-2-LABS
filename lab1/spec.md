## User
- userID - PK - int
- username - UK - string
- email - UK - string
- password - - string
- created_at -  - datetime

## Problem
- problemID - PK - int
- title - UK - string
- description - - string
- difficulty - - string
- is_premium - - boolean

## Tag
- tagID - PK - int
- name - UK - string
- category - - string

## Submission
- submissionID - PK - int
- userID - FK - int
- problemID - FK - int
- languageID - FK - int
- code - - string
- status - - string
- runtime_ms - - int
- memory_kb - - int
- submitted_at - - datetime

## Language
- languageID - PK - int
- name - UK - string 

## TestCase
- testCaseID - PK - int
- problemID - FK - int
- input_code - -string
- expected_output - - string

## EditorialPost
- EditorialPostID - PK - int
- userID - FK - int
- problemID - FK - int
- languageID - FK - int
- submissionID - FK - int
- title - - string



## Relationships

# User — Submission (1:N, один-до-багатьох):**
  - Один користувач може надіслати багато розв'язків (0..*).
  - Кожне подання належить рівно одному користувачеві (1..1).
  - Зовнішній ключ: `Submission.userID` посилається на `User.userID`.

# Problem — Submission (1:N, один-до-багатьох):**
  - До однієї задачі може бути створено багато подань (0..*).
  - Кожне подання стосується виключно однієї задачі (1..1).
  - Зовнішній ключ: `Submission.problemID` посилається на `Problem.problemID`.

# Language — Submission (1:N, один-до-багатьох):**
  - Одна мова програмування використовується у багатьох поданнях (0..*).
  - Кожне окреме подання написане однією мовою (1..1).
  - Зовнішній ключ: `Submission.languageID` посилається на `Language.languageID`.

# Problem — TestCase (1:N, один-до-багатьох):**
  - Одна задача має один або більше тест-кейсів для перевірки (1..*).
  - Кожен тест-кейс належить виключно одній конкретній задачі (1..1).
  - Зовнішній ключ: `TestCase.problemID` посилається на `Problem.problemID`.

# Problem — Tag (M:N, багато-до-багатьох):**
  - Одна задача може мати декілька тегів категорій (0..*).
  - Один тег може належати довільній кількості задач (0..*).
  - Зв'язок прямий концептуальний, без створення проміжної сполучної таблиці.

# User — EditorialPost (1:N, один-до-багатьох):**
  - Один користувач може опублікувати багато дописів із розбором (0..*).
  - Кожен допис має рівно одного автора (1..1).
  - Зовнішній ключ: `EditorialPost.userID` посилається на `User.userID`.

# Submission — EditorialPost (1:1, один-до-одного):**
  - Конкретне подання розв'язку може бути використане для створення одного авторського розбору (0..1).
  - Кожен розбір базується на конкретному успішному поданні (1..1).
  - Зовнішній ключ: `EditorialPost.submissionID` посилається на `Submission.submissionID`.