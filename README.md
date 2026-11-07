# Finance Tracker

A Python/FastAPI application for recording expenses, setting monthly category
budgets, and viewing spending summaries. It serves an HTML frontend and exposes
an API backed by SQLAlchemy with SQLite or PostgreSQL.

## Features

- Expense creation, editing, deletion, and category/month/year filtering.
- Monthly category budgets and spending summaries.
- Password-based bearer-token authentication and optional email OTP flows.
- An HTML frontend and interactive API documentation.

**Stack:** Python · FastAPI · SQLAlchemy · SQLite / PostgreSQL

[API task guide](docs/first-expense.md) · [Verification](docs/verification.md) ·
[Configuration handling](SECURITY.md)

## Start locally with SQLite

Requires Python and pip. The repository's `runtime.txt` specifies Python 3.11.
Run these commands in PowerShell from the repository root:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Copy-Item .env.example .env
```

Edit `.env` and replace `SECRET_KEY` with a random value generated locally:

```powershell
.\.venv\Scripts\python.exe -c "import secrets; print(secrets.token_urlsafe(32))"
.\.venv\Scripts\python.exe -m uvicorn app.main:app --host 127.0.0.1 --port 8000
```

Open `http://127.0.0.1:8000/` for the frontend, `/docs` for the interactive API
reference, and `/health` to confirm the server responds. The health response is
`{"status":"healthy","message":"Session verification active"}`; this endpoint
does not test the mail provider or all database operations.

Tables are created on application import. Run from the root so `.env` and `static/`
resolve correctly. Keep the server bound to localhost for development.

## Try an API task without email delivery

Follow [Record your first expense](docs/first-expense.md). Password-based API
login works independently of OTP delivery. Frontend OTP flows require a configured
Resend account and a permitted sender/recipient configuration.

## Configuration

| Setting | Purpose |
| --- | --- |
| `DATABASE_URL` | SQLAlchemy connection URL; the example uses a local SQLite file |
| `SECRET_KEY` | Signs login tokens; replace the example value |
| `ALGORITHM` | Token algorithm, default `HS256` |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Token lifetime, default 60 minutes |
| `RESEND_API_KEY` | Optional for the password-based API task; required for email OTP |

The effective mail helper currently uses Resend's onboarding sender. SMTP-related
settings exist in configuration but are not used by that helper. Do not assume
setting `MAIL_FROM` changes the sender.

## API overview

| Area | Routes |
| --- | --- |
| Accounts | `/auth/register`, `/auth/login`, `/auth/send-otp`, `/auth/verify-otp`, `/auth/reset-password` |
| Expenses | `GET/POST /expenses/`, `PUT/DELETE /expenses/{expense_id}` |
| Budgets | `GET/POST /budgets/`, `DELETE /budgets/{budget_id}` |
| Reports | `GET /reports/monthly-summary?year=2026&month=11`, `GET /reports/spending-trend` |

Expense, budget, and report routes require a bearer token. Login uses form fields
`username` (the registered email) and `password`, not JSON.

## Troubleshooting and limits

- Missing settings: copy `.env.example` to `.env` and set the required values.
- Database errors: check the URL and local file permissions before changing data.
- OTP failures: verify Resend configuration. Password API login can test the core workflow without sending mail.
- Docker Compose is incomplete: it references a Dockerfile not present in this repository. Use the local setup above.
- Requirements are not fully pinned. Reproducibility across Python versions needs further validation.
- This is a portfolio application, not a claim of production security or financial suitability.

Keep credentials, personal records, and local database files out of commits.
See [verification](docs/verification.md) for what was actually tested.
