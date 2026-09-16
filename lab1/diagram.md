```mermaid
erDiagram
    erDiagram
    USER {
        int userID PK
        string username UK
        string email UK
        string password
        datetime created_at
        boolean has_premium
    }

    Problem {
        int problemID PK
        string title UK
       string description
       string difficulty
       boolean is_premium
    }

    Tag {
       int tagID PK
string name UK
string category
    }

    Submission {
        int submissionID PK
int userID FK
int problemID FK
int languageID FK
string code
string status
int runtime_ms
int memory_kb
datetime submitted_at
    }

Language {
int languageID PK
string name UK
}

TestCase {
int testCaseID PK
int problemId FK
string input_code
string expected_output
}

EditorialPost {
int editorialPostID PK
int userID FK
int problemID FK
int languageID FK
int submissionID FK
string title


}


    USER ||--o{ Submission : "submits"
USER ||--o{ EditorialPost : "posts"
Problem ||--o{Submission : ""
Language ||--o{Submission : ""
Problem ||--o{TestCase : ""
Problem }o--o{Tag : ""
Submission o|--o|EditorialPost : ""
   
```