## Testing connection with postgres

Update postgres password
```shell
sudo passwd postgres
```

To start postgres

```shell
sudo service postgresql start
```

To run postgres
```shell
sudo -u postgres psql
```
to alter password

```shell
ALTER USER postgres PASSWORD 'xxxxxx';
```

## Migrations (Using Goose)

For DB migrations use "Goose", install using

```shell
go install github.com/pressly/goose/v3/cmd/goose@latest
```

Goose migration scripts are stored in `sql/schema` and have the following format

```sql
-- +goose Up
CREATE TABLE ...

-- +goose Down
DROP TABLE users;
```

test connection to database from terminal as follows (Note DATABASE is chirpy)

```shell
psql "postgres://PGUSER:PGPASS@localhost:5432/DATABASE"
```

To run in migrations use the following command (not these will need to be executed directly in the sql/schema folder)

```shell
cd sql/schema
goose postgres "postgres://PGUSER:PGPASS@localhost:5432/DATABASE" up
```

Output should be shomething like
```shell
202X/XX/XX 09:46:48 OK   002_chirps.sql (31.55ms)
202X/XX/XX 09:46:48 goose: successfully migrated database to version: 2
```

or for dropping schema

```shell
cd sql/schema
goose postgres "postgres://PGUSER:PGPASS@localhost:5432/DATABASE" down
```

For this project the database is designated as 'chirpy'

## UUID Library (Required for sqlc generation)

For UUID generation the following libraries is required

```shell
go get github.com/google/uuid
```

## PostgreSQL driver library

Required to run the project and for migrations

```shell
go get github.com/lib/pq
```

Note to use you'll need this at the top of main.go

```go
import _ "github.com/lib/pq"
```

## Go SQL Code generation (using SQLC)

SQLC is a Go program that generates Go code from SQL queries. Install using

```shell
go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest
```

For configuration create a "sqlc.yaml" file in the root folder with content

```yaml
version: "2"
sql:
  - schema: "sql/schema"
    queries: "sql/queries"
    engine: "postgresql"
    gen:
      go:
        out: "internal/database"
```

now create a query and store in sql/queries, here is an example:

```sql
-- name: CreateUser :one
INSERT INTO users (id, created_at, updated_at, email)
VALUES (
    gen_random_uuid(),
    NOW(),
    NOW(),
    $1
)
RETURNING *;
```

Notes:
- The `:one` at the end of the query name tells SQLC that we expect to get back a single row (the created user).
- Use `$1` to represent the first parameter passed into the query

SQLC Reference Docs can be found here: https://docs.sqlc.dev/en/latest/tutorials/getting-started-postgresql.html

In order to run a `.env` file is required with the following content (update as needed)
is needed in root of project

```shell
DB_URL="postgres://PGUSER:PGPASS@localhost:5432/DATABASE"
PLATFORM="dev"
```

To create Go SQL code, in the root of the project run

```shell
sqlc generate
```

## Debugging using VSCode

Create the following file for debugging

.vscode/launch.json
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
    
        {
            "name": "Launch Package",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            "program": "${fileDirname}"
        }
    ]
}
```

## Other libraries

```shell
go get golang.org/x/crypto
```

```shell
go get -u github.com/golang-jwt/jwt/v5
```


After making changes to code its a good practice to run

```shell
go mod tidy
```

