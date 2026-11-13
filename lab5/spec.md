# Component Specification

## 1. System purpose

Платформа дозволяє користувачам переглядати задачі,
надсилати рішення, отримувати результати перевірки,
публікувати editorial та купувати собі або іншому користувачу Premium.

## 2. System boundary

### Inside the system

- Web UI
- API / Backend
- Authentication Service
- Problem Service
- Submission Service
- Judge Service
- Editorial Service
- Premium Service
- Data Access Layer
- Application Database

### Outside the system

- Payment Service
- OAuth / Identity Provider
- Email/SMS Service
- Code Runner / Sandbox

## 3. Provided interfaces

| Interface | Provider | Consumers |
|---|---|---|
| IWebApi | API / Backend | Web UI |
| IAuthentication | Authentication Service | API |
| IProblemManagement | Problem Service | API |
| IJudge | Judge Service | Submission Service |
| IPaymentGateway | Payment Service | Premium Service |

## 4. Required interfaces

| Consumer | Required interface | Provider |
|---|---|---|
| Web UI | IWebApi | API / Backend |
| API / Backend | IAuthentication | Authentication Service |
| API / Backend | IProblemManagement | Problem Service |
| Submission Service | IJudge | Judge Service |
| Premium Service | IPaymentGateway | Payment Service |

## 5. Component responsibilities

| Component | Responsibility |
|---|---|
| Web UI | Displays problems, submission forms, check results, editorials and Premium operations |
| API / Backend | Exposes the public API and coordinates authentication, problems, submissions, editorials and Premium operations |
| Authentication Service | Authenticates users through the identity provider and sends authentication notifications |
| Problem Service | Provides problem statements and manages problem-related data |
| Submission Service | Accepts solutions, stores submissions and requests their evaluation |
| Judge Service | Evaluates submitted solutions and returns checking results |
| Editorial Service | Publishes and provides editorials for problems |
| Premium Service | Creates and manages Premium purchases for the current user or another user |
| Data Access Layer | Encapsulates persistence operations for application services |
| Application Database | Stores users, problems, submissions, judging results, editorials and Premium data |

## 6. Internal dependencies

| Consumer | Dependency | Purpose |
|---|---|---|
| API / Backend | Submission Service | Accept and process solution submissions |
| API / Backend | Editorial Service | Manage and retrieve editorials |
| API / Backend | Premium Service | Manage Premium purchases |
| Authentication Service | Data Access Layer | Persist and retrieve user authentication data |
| Problem Service | Data Access Layer | Persist and retrieve problems |
| Submission Service | Data Access Layer | Persist submissions and checking results |
| Submission Service | IJudge | Request solution evaluation |
| Judge Service | Data Access Layer | Read evaluation data and store results |
| Editorial Service | Data Access Layer | Persist and retrieve editorials |
| Premium Service | Data Access Layer | Persist Premium purchase data |
| Data Access Layer | Application Database | Read and write application data |

## 7. External integrations

| External component | Connected component | Purpose |
|---|---|---|
| OAuth / Identity Provider | Authentication Service | User authentication and identity verification |
| Email/SMS Service | Authentication Service | Authentication and account notifications |
| Code Runner / Sandbox | Judge Service | Isolated execution of submitted code |
| Payment Service | Premium Service | Processing Premium payments through IPaymentGateway |

`IPaymentGateway` is an integration contract at the system boundary: it is
provided by the external Payment Service and required by the Premium Service.