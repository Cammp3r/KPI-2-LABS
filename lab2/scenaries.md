Feature: LeetCode-подібна платформа

  Rule: Каталог задач

    @REQ-01 @REQ-02
    Scenario: SC-01 Фільтрація задач за складністю та тегом
      Given у каталозі є задача "Two Sum" зі складністю "Easy" і тегом "Array"
      And у каталозі є задача "3Sum" зі складністю "Medium" і тегом "Array"
      And у каталозі є задача "Valid Anagram" зі складністю "Easy" і тегом "Hash Table"
      When користувач обирає складність "Easy" і тег "Array"
      Then у каталозі відображається лише задача "Two Sum"
      And для задачі відображаються її назва, складність і теги

  Rule: Реєстрація та вхід

    @REQ-04
    Scenario: SC-02 Успішна реєстрація
      Given користувача з username "alex" або email "alex@mail.com" не існує
      When гість реєструється з username "alex", email "alex@mail.com" і паролем "Secret123"
      Then створюється USER з username "alex", role "user" і has_premium = false
      And у полі password_hash зберігається хеш, а не пароль "Secret123"

    @REQ-03
    Scenario: SC-03 Реєстрація з уже зайнятим email
      Given існує користувач з email "alex@mail.com"
      When гість реєструється з username "newuser", email "alex@mail.com" і паролем "Secret123"
      Then реєстрацію відхилено
      And система повідомляє, що поле "email" вже зайняте
      And новий USER не створюється

    @REQ-05
    Scenario: SC-04 Успішний вхід за email і паролем
      Given існує користувач "alex" з email "alex@mail.com" і паролем "Secret123"
      When гість входить з email "alex@mail.com" і паролем "Secret123"
      Then користувач авторизований як "alex"

    @REQ-06
    Scenario: SC-05 Вхід з неправильним паролем
      Given існує користувач з email "alex@mail.com" і паролем "Secret123"
      When гість входить з email "alex@mail.com" і паролем "Wrong999"
      Then вхід відхилено
      And система показує повідомлення "Невірний email або пароль"
      And користувач залишається неавторизованим

  Rule: Надсилання рішень

    @REQ-07 @REQ-08 @REQ-09
    Scenario: SC-06 Правильне рішення отримує статус Accepted
      Given користувач "alex" авторизований
      And у задачі "Two Sum" є 3 тест-кейси
      And у таблиці Language є мова "Python"
      When користувач обирає мову "Python" і надсилає рішення, що проходить усі 3 тест-кейси
      Then створюється Submission для "alex" і задачі "Two Sum" з мовою "Python" і статусом "Pending"
      And після перевірки Submission отримує статус "Accepted"
      And у Submission збережено runtime_ms і memory_kb

    @REQ-09 @REQ-11
    Scenario: SC-07 Неправильне рішення отримує статус Wrong Answer
      Given користувач "alex" авторизований
      And у задачі "Two Sum" тест-кейс №2 має вхідні дані "[3,2,4], 6" і очікуваний вивід "[1,2]"
      When користувач надсилає рішення, яке проходить тест-кейс №1, а на тест-кейсі №2 повертає "[0,2]"
      Then Submission отримує статус "Wrong Answer"
      And система показує вхідні дані "[3,2,4], 6", очікуваний вивід "[1,2]" і фактичний вивід "[0,2]"

    @REQ-09 @REQ-10
    Scenario: SC-08 Рішення перевищує ліміт часу
      Given користувач "alex" авторизований
      When користувач надсилає рішення, яке на одному з тест-кейсів виконується довше за 2000 мс
      Then перевірку зупинено
      And Submission отримує статус "Time Limit Exceeded"

    @REQ-12
    Scenario: SC-09 Перегляд історії надсилань
      Given користувач "alex" авторизований
      And у "alex" є Submission від 01.10 зі статусом "Wrong Answer"
      And у "alex" є Submission від 02.10 зі статусом "Accepted"
      And у користувача "maria" є власні Submission
      When "alex" відкриває історію надсилань
      Then відображаються лише Submission користувача "alex"
      And першим відображається Submission від 02.10
      And для кожного Submission показано статус, мову, runtime_ms і дату

  Rule: Розбори

    @REQ-14
    Scenario: SC-10 Успішна публікація розбору
      Given користувач "alex" авторизований
      And у "alex" є Submission для задачі "Two Sum" зі статусом "Accepted"
      And для цього Submission ще немає EditorialPost
      When "alex" публікує розбір "Hash map за O(n)" на основі цього Submission
      Then створюється EditorialPost з title "Hash map за O(n)"
      And EditorialPost пов'язаний з "alex", задачею "Two Sum" і цим Submission

    @REQ-14
    Scenario: SC-11 Розбір на основі неприйнятого рішення
      Given користувач "alex" авторизований
      And у "alex" є Submission для задачі "Two Sum" зі статусом "Wrong Answer"
      When "alex" публікує розбір на основі цього Submission
      Then публікацію відхилено
      And EditorialPost не створюється

    @REQ-15
    Scenario: SC-12 Повторний розбір для того самого рішення
      Given користувач "alex" авторизований
      And у "alex" є Submission зі статусом "Accepted", для якого вже існує EditorialPost
      When "alex" публікує ще один розбір на основі цього Submission
      Then публікацію відхилено
      And для цього Submission залишається лише один EditorialPost

  Rule: Premium

    @REQ-16
    Scenario: SC-13 Успішна оплата Premium
      Given користувач "alex" авторизований
      And у "alex" has_premium = false
      When "alex" оформлює Premium
      And платіжний сервіс підтверджує успішну оплату
      Then у "alex" has_premium = true

    @REQ-17 @REQ-18
    Scenario: SC-14 Відмова в оплаті Premium
      Given користувач "alex" авторизований
      And у "alex" has_premium = false
      When "alex" оформлює Premium
      And платіжний сервіс повідомляє про відмову з причиною "Недостатньо коштів"
      Then у "alex" залишається has_premium = false
      And система показує причину відмови "Недостатньо коштів"
      And опис задач з is_premium = true для "alex" залишається заблокованим

    @REQ-18
    Scenario: SC-15 Користувач без Premium відкриває Premium-задачу
      Given користувач "alex" авторизований
      And у "alex" has_premium = false
      And у каталозі є задача "LRU Cache" з is_premium = true
      When "alex" відкриває задачу "LRU Cache"
      Then задача відображається в каталозі
      But опис задачі та форма надсилання рішення заблоковані

    @REQ-19
    Scenario: SC-16 Premium-користувач відкриває Premium-задачу
      Given користувач "maria" авторизований
      And у "maria" has_premium = true
      And у каталозі є задача "LRU Cache" з is_premium = true
      When "maria" відкриває задачу "LRU Cache"
      Then відображається опис задачі
      And доступна форма надсилання рішення

  Rule: Адміністрування

    @REQ-20
    Scenario: SC-17 Збереження задачі без тест-кейсів
      Given користувач "admin" авторизований з role "admin"
      When "admin" зберігає нову задачу "Merge Intervals" без жодного тест-кейсу
      Then збереження відхилено
      And задача "Merge Intervals" не з'являється в каталозі

    @REQ-21
    Scenario: SC-18 Користувач без ролі admin створює задачу
      Given користувач "alex" авторизований з role "user"
      When "alex" намагається створити задачу "Merge Intervals"
      Then дію відхилено
      And задача "Merge Intervals" не з'являється в каталозі
