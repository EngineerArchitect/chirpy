## Migrations

For DB migrations use "Goose", install using

```shell
go install github.com/pressly/goose/v3/cmd/goose@latest
```

Goose migration scripts are stored in `sql/schema`

To run in migrations use the following command (not these will need to be executed directly in the sql/schema folder)

```shell
goose postgres "postgres://PGUSER:PGPASS@localhost:5432/DATABASE" up
```

or for dropping schema

```shell
goose postgres "postgres://PGUSER:PGPASS@localhost:5432/DATABASE" down
```

For this project the database is designated as 'chirpy'

## Go SQL Code generation

SQLC is a Go program that generates Go code from SQL queries. Install using

```shell
go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest
```

In order to run a ".env" file is required with the following content (update as needed)

```
DB_URL="postgres://PGUSER:PGPASS@localhost:5432/DATABASE"
PLATFORM="dev"
```

To create Go SQL code, in the root of the project run

```
sqlc generate
```