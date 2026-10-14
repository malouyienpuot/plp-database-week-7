# PLP Database Week 7

PostgreSQL schema, sample data, row-level auditing, category hierarchy, and
application-role setup for the bootcamp database.

## Requirements

- PostgreSQL 11 or newer
- `psql` command-line client
- A database named `bootcamp` (or update the database name in
	`setup_security.sql`)

## Apply migrations

Connect to the target database and run the migration files in order. For
example, from the repository root:

```powershell
psql -d bootcamp -v ON_ERROR_STOP=1 -f migrations/V1__core_tables.sql
psql -d bootcamp -v ON_ERROR_STOP=1 -f migrations/V2__audit_log.sql
psql -d bootcamp -v ON_ERROR_STOP=1 -f migrations/V3__categories.sql
```

The schema creation and seed inserts are safe to rerun. The migration files
must be applied in version order because later migrations depend on earlier
tables.

## Set up access roles

Run the security script as a PostgreSQL administrator connected to `bootcamp`:

```powershell
psql -d bootcamp -v ON_ERROR_STOP=1 -f setup_security.sql
```

The script creates the non-login `app_read` and `app_write` roles and the
login role `api` if they do not already exist. It does not store a password in
the repository. Set the `api` password interactively in `psql` with `\password api`
before using that account.

Default privileges only affect objects created by the role that runs
`setup_security.sql`; run it as the schema/object owner if those grants should
cover future migrations. Re-run it after creating objects owned by a different
role, or configure that owner's default privileges separately.

## Schema overview

- `students`: student records with unique, non-empty email addresses.
- `audit_log`: old and new row snapshots for student inserts, updates, and
  deletes, including the database user and timestamp.
- `categories`: self-referencing category hierarchy with repeatable sample
  data.
