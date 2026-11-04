# Record your first expense through the API

Use this procedure to verify registration, password login, and expense creation
with synthetic data. Start the local server using the [quickstart](../README.md).

## Register and sign in

Open a second PowerShell terminal. Use a new synthetic email for each run:

```powershell
$baseUrl = 'http://127.0.0.1:8000'
$email = "demo-$([guid]::NewGuid().ToString('N'))@example.com"
$password = 'LocalDemo-Only-123!'
$registration = @{email=$email; username='Demo reader'; password=$password} | ConvertTo-Json
Invoke-RestMethod "$baseUrl/auth/register" -Method Post -ContentType 'application/json' -Body $registration
$session = Invoke-RestMethod "$baseUrl/auth/login" -Method Post -Body @{username=$email; password=$password}
$headers = @{Authorization="Bearer $($session.access_token)"}
```

Registration returns a user ID. Login returns `access_token` and `token_type`.
This password-based API path does not send email or verify email ownership.

## Create and check an expense

```powershell
$expense = @{amount=125.5; category='food'; description='Synthetic lunch example'; date='2026-11-04T12:00:00'} | ConvertTo-Json
$created = Invoke-RestMethod "$baseUrl/expenses/" -Method Post -Headers $headers -ContentType 'application/json' -Body $expense
$created
Invoke-RestMethod "$baseUrl/expenses/?month=11&year=2026" -Headers $headers
Invoke-RestMethod "$baseUrl/reports/monthly-summary?year=2026&month=11" -Headers $headers
```

The created expense should have an ID, amount `125.5`, category `food`, and the
supplied date. The list should include that ID. For a fresh account, the summary
should report `125.5` spent on food.

Supported categories are `food`, `transport`, `utilities`, `entertainment`, `health`,
`shopping`, and `other`. Do not send arbitrary category names.

## Recover from errors

For `401`, sign in again and rebuild the bearer header. For an already registered
email, use the same password to sign in or create a different synthetic account.
For `422`, compare the request fields with `/docs` and verify the date format.

## Remove the example expense

The following deletes only the expense returned in `$created`. Confirm its ID first:

```powershell
$created.id
Invoke-RestMethod "$baseUrl/expenses/$($created.id)" -Method Delete -Headers $headers
```

This leaves the synthetic account in the local database. Use an isolated test
database when validating documentation rather than a database with personal records.
