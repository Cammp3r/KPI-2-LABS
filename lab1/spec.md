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
- name - - string

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

