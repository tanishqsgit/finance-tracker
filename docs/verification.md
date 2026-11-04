# Quickstart verification

Date: 4 November 2026.

An agent-executed local HTTP smoke check used Python 3.13, dependencies installed
from `requirements.txt`, a temporary SQLite database, a random test signing secret,
and synthetic account data.

Passed: server startup, health response, HTML frontend response, interactive API
documentation, registration, password login, expense creation, month/year filtering,
monthly summary, expense deletion, and an empty list after deletion.

The requests exercised the documented API sequence. The exact PowerShell commands
were not executed verbatim, and the frontend's interactive OTP workflow, external
email delivery, PostgreSQL, Docker, and hosted deployment were not tested. The
repository's declared Python 3.11 runtime was not available for this run.

The guide was drafted with AI assistance. Human product review remains pending.
See [configuration handling](../SECURITY.md) for the previously tracked files.
