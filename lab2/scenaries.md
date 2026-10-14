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

    @REQ-03
    Scenario: SC-02 Розв'язана задача позначена в каталозі
      Given користувач "alex" авторизований
      And у "alex" є Submission для задачі "Two Sum" зі статусом "Accepted"
      And у каталозі є задача "Two Sum"
      And у каталозі є задача "3Sum", яку "alex" ще не розв'язав
      When "alex" відкриває каталог задач
      Then задача "Two Sum" позначена як розв'язана
      And задача "3Sum" не позначена як розв'язана

  Rule: Реєстрація та вхід

    @REQ-05
    Scenario: SC-03 Успішна реєстрація
      Given користувача з username "alex" або email "alex@mail.com" не існує
      When гість реєструється з username "alex", email "alex@mail.com" і паролем "Secret123"
      Then створюється USER з username "alex", role "user" і has_premium = false
      And у полі password_hash зберігається хеш, а не пароль "Secret123"

    @REQ-04
    Scenario: SC-04 Реєстрація з уже зайнятим email
      Given існує користувач з email "alex@mail.com"
      When гість реєструється з username "newuser", email "alex@mail.com" і паролем "Secret123"
      Then реєстрацію відхилено
      And система повідомляє, що поле "email" вже зайняте
      And новий USER не створюється

    @REQ-06
    Scenario: SC-05 Успішний вхід за email і паролем
      Given існує користувач "alex" з email "alex@mail.com" і паролем "Secret123"
      When гість входить з email "alex@mail.com" і паролем "Secret123"
      Then користувач авторизований як "alex"

    @REQ-07
    Scenario: SC-06 Вхід з неправильним паролем
      Given існує користувач з email "alex@mail.com" і паролем "Secret123"
      When гість входить з email "alex@mail.com" і паролем "Wrong999"
      Then вхід відхилено
      And система показує повідомлення "Невірний email або пароль"
      And користувач залишається неавторизованим

  Rule: Надсилання рішень

    @REQ-08 @REQ-09 @REQ-10
    Scenario: SC-07 Правильне рішення отримує статус Accepted
      Given користувач "alex" авторизований
      And у задачі "Two Sum" є 3 тест-кейси
      And у таблиці Language є мова "Python"
      When користувач обирає мову "Python" і надсилає рішення, що проходить усі 3 тест-кейси
      Then створюється Submission для "alex" і задачі "Two Sum" з мовою "Python" і статусом "Pending"
      And після перевірки Submission отримує статус "Accepted"
      And у Submission збережено runtime_ms і memory_kb

    @REQ-10 @REQ-12
    Scenario: SC-08 Неправильне рішення отримує статус Wrong Answer
      Given користувач "alex" авторизований
      And у задачі "Two Sum" тест-кейс №2 має вхідні дані "[3,2,4], 6" і очікуваний вивід "[1,2]"
      When користувач надсилає рішення, яке проходить тест-кейс №1, а на тест-кейсі №2 повертає "[0,2]"
      Then Submission отримує статус "Wrong Answer"
      And система показує вхідні дані "[3,2,4], 6", очікуваний вивід "[1,2]" і фактичний вивід "[0,2]"

    @REQ-10 @REQ-11
    Scenario: SC-09 Рішення перевищує ліміт часу
      Given користувач "alex" авторизований
      When користувач надсилає рішення, яке на одному з тест-кейсів виконується довше за 2000 мс
      Then перевірку зупинено
      And Submission отримує статус "Time Limit Exceeded"

    @REQ-13
    Scenario: SC-10 Перегляд історії надсилань
      Given користувач "alex" авторизований
      And у "alex" є Submission від 01.10 зі статусом "Wrong Answer"
      And у "alex" є Submission від 02.10 зі статусом "Accepted"
      And у користувача "maria" є власні Submission
      When "alex" відкриває історію надсилань
      Then відображаються лише Submission користувача "alex"
      And першим відображається Submission від 02.10
      And для кожного Submission показано статус, мову, runtime_ms і дату

  Rule: Складність рішення

    @REQ-15 @REQ-16
    Scenario: SC-25 Підрахунок time та space complexity після Accepted
      Given користувач "alex" авторизований
      And у задачі "Two Sum" є 3 тест-кейси
      When користувач надсилає рішення на Python з одним проходом по масиву та хеш-мапою, що проходить усі 3 тест-кейси
      Then Submission отримує статус "Accepted"
      And у Submission збережено time_complexity "O(n)" і space_complexity "O(n)"
      And користувачу показано time_complexity "O(n)" і space_complexity "O(n)" поруч із runtime_ms і memory_kb

    @REQ-17
    Scenario: SC-26 Складність не обчислюється для неприйнятого рішення
      Given користувач "alex" авторизований
      When користувач надсилає рішення, яке на одному з тест-кейсів повертає неправильний вивід
      Then Submission отримує статус "Wrong Answer"
      And time_complexity і space_complexity у Submission залишаються порожніми
      And користувачу не показується оцінка складності

    @REQ-16
    Scenario: SC-27 Складність видно в історії надсилань
      Given користувач "alex" авторизований
      And у "alex" є Submission від 02.10 зі статусом "Accepted", time_complexity "O(n)" і space_complexity "O(n)"
      And у "alex" є Submission від 01.10 зі статусом "Wrong Answer"
      When "alex" відкриває історію надсилань
      Then для Submission від 02.10 показано time_complexity "O(n)" і space_complexity "O(n)"
      And для Submission від 01.10 оцінка складності не показується

  Rule: Розбори

    @REQ-18
    Scenario: SC-11 Успішна публікація розбору
      Given користувач "alex" авторизований
      And у "alex" є Submission для задачі "Two Sum" зі статусом "Accepted"
      And для цього Submission ще немає EditorialPost
      When "alex" публікує розбір "Hash map за O(n)" на основі цього Submission
      Then створюється EditorialPost з title "Hash map за O(n)"
      And EditorialPost пов'язаний з "alex", задачею "Two Sum" і цим Submission

    @REQ-18
    Scenario: SC-12 Розбір на основі неприйнятого рішення
      Given користувач "alex" авторизований
      And у "alex" є Submission для задачі "Two Sum" зі статусом "Wrong Answer"
      When "alex" публікує розбір на основі цього Submission
      Then публікацію відхилено
      And EditorialPost не створюється

    @REQ-19
    Scenario: SC-13 Повторний розбір для того самого рішення
      Given користувач "alex" авторизований
      And у "alex" є Submission зі статусом "Accepted", для якого вже існує EditorialPost
      When "alex" публікує ще один розбір на основі цього Submission
      Then публікацію відхилено
      And для цього Submission залишається лише один EditorialPost

  Rule: Premium

    @REQ-20
    Scenario: SC-14 Успішна оплата Premium собі
      Given користувач "alex" авторизований
      And у "alex" has_premium = false
      When "alex" оформлює Premium для себе
      And платіжний сервіс підтверджує успішну оплату
      Then у "alex" has_premium = true

    @REQ-20
    Scenario: SC-15 Успішне оформлення Premium у подарунок
      Given користувач "alex" авторизований
      And у "alex" has_premium = false
      And існує користувач "maria" з has_premium = false
      When "alex" оформлює Premium у подарунок для "maria"
      And платіжний сервіс підтверджує успішну оплату
      Then у "maria" has_premium = true
      And у "alex" залишається has_premium = false

    @REQ-21 @REQ-24
    Scenario: SC-16 Відмова в оплаті Premium (первинна)
      Given користувач "alex" авторизований
      And у "alex" has_premium = false
      When "alex" оформлює Premium
      And платіжний сервіс повідомляє про відмову з причиною "Недостатньо коштів"
      Then у "alex" залишається has_premium = false
      And система показує причину відмови "Недостатньо коштів"
      And опис задач з is_premium = true для "alex" залишається заблокованим

    @REQ-22
    Scenario: SC-17 Відмова при поновленні Premium — підписка залишається активною
      Given користувач "alex" авторизований
      And у "alex" has_premium = true
      When настає дата щомісячного списання коштів за Premium
      And платіжний сервіс повідомляє про відмову з причиною "Недостатньо коштів"
      Then у "alex" has_premium = true
      And система зберігає дату невдалої оплати
      And система показує причину відмови "Недостатньо коштів"

    @REQ-23
    Scenario: SC-18 Premium скасовується через тиждень неоплати
      Given у "alex" has_premium = true
      And останню невдалу спробу списання коштів за Premium для "alex" зафіксовано 7 днів тому
      And оплата досі не проведена успішно
      When система перевіряє статус Premium-підписок
      Then у "alex" has_premium = false

    @REQ-24
    Scenario: SC-19 Користувач без Premium відкриває Premium-задачу
      Given користувач "alex" авторизований
      And у "alex" has_premium = false
      And у каталозі є задача "LRU Cache" з is_premium = true
      When "alex" відкриває задачу "LRU Cache"
      Then задача відображається в каталозі
      But опис задачі та форма надсилання рішення заблоковані

    @REQ-25
    Scenario: SC-20 Premium-користувач відкриває Premium-задачу
      Given користувач "maria" авторизований
      And у "maria" has_premium = true
      And у каталозі є задача "LRU Cache" з is_premium = true
      When "maria" відкриває задачу "LRU Cache"
      Then відображається опис задачі
      And доступна форма надсилання рішення

    @REQ-26
    Scenario: SC-21 Подарунок неіснуючому користувачу
      Given користувач "alex" авторизований
      And користувача з username "ghost" не існує
      When "alex" оформлює Premium у подарунок для "ghost"
      Then оформлення відхилено
      And запит до платіжного сервісу не надсилається

    @REQ-27
    Scenario: SC-22 Подарунок користувачу, який уже має Premium
      Given користувач "alex" авторизований
      And існує користувач "maria" з has_premium = true
      When "alex" оформлює Premium у подарунок для "maria"
      Then оформлення відхилено
      And система повідомляє, що "maria" уже має Premium
      And запит до платіжного сервісу не надсилається

  Rule: Адміністрування

    @REQ-28
    Scenario: SC-23 Збереження задачі без тест-кейсів
      Given користувач "admin" авторизований з role "admin"
      When "admin" зберігає нову задачу "Merge Intervals" без жодного тест-кейсу
      Then збереження відхилено
      And задача "Merge Intervals" не з'являється в каталозі

    @REQ-29
    Scenario: SC-24 Користувач без ролі admin створює задачу
      Given користувач "alex" авторизований з role "user"
      When "alex" намагається створити задачу "Merge Intervals"
      Then дію відхилено
      And задача "Merge Intervals" не з'являється в каталозі
