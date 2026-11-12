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