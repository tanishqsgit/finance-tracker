# Handle local configuration and data

Use `.env.example` as a template. Never commit real `.env` files, API credentials,
database URLs containing passwords, or personal financial records.

The portfolio documentation update removes a previously tracked `.env` and SQLite
database from the current tree. Copies remain in earlier Git history. Treat any
credentials previously stored there as exposed: rotate them at their providers,
replace local configuration, and invalidate affected sessions as appropriate.
Deleting a file in a new commit does not revoke a credential or erase history.

Repository-history cleanup requires separate coordination with existing clones.
No history rewrite or provider-side credential rotation was performed by this update.
