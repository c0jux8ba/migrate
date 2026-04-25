# migrate

A fork of [golang-migrate/migrate](https://github.com/golang-migrate/migrate) — Database migrations written in Go. Use as CLI or import as library.

[![Go Report Card](https://goreportcard.com/badge/github.com/your-org/migrate)](https://goreportcard.com/report/github.com/your-org/migrate)
[![CI](https://github.com/your-org/migrate/actions/workflows/ci.yaml/badge.svg)](https://github.com/your-org/migrate/actions/workflows/ci.yaml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Features

- Supports multiple database drivers (PostgreSQL, MySQL, SQLite, MongoDB, and more)
- Supports multiple migration source formats (filesystem, Go embed, S3, GitHub, etc.)
- CLI and library usage
- Up/down migrations
- Version tracking
- Dirty state detection and recovery

## Supported Databases

| Database   | Driver Import Path                          |
|------------|---------------------------------------------|
| PostgreSQL | `github.com/your-org/migrate/database/postgres` |
| MySQL      | `github.com/your-org/migrate/database/mysql`    |
| SQLite3    | `github.com/your-org/migrate/database/sqlite3`  |
| MongoDB    | `github.com/your-org/migrate/database/mongodb`  |

## Installation

### CLI

```bash
go install github.com/your-org/migrate/cmd/migrate@latest
```

Or download a pre-built binary from the [releases page](https://github.com/your-org/migrate/releases).

### Library

```bash
go get github.com/your-org/migrate/v4
```

## CLI Usage

```bash
# Run all pending up migrations
migrate -path ./migrations -database "postgres://localhost:5432/mydb?sslmode=disable" up

# Roll back the last migration
migrate -path ./migrations -database "postgres://localhost:5432/mydb?sslmode=disable" down 1

# Show current migration version
migrate -path ./migrations -database "postgres://localhost:5432/mydb?sslmode=disable" version

# Force set migration version (use to fix dirty state)
migrate -path ./migrations -database "postgres://localhost:5432/mydb?sslmode=disable" force 3

# Roll back ALL migrations (use with caution in production!)
migrate -path ./migrations -database "postgres://localhost:5432/mydb?sslmode=disable" down -all
```

## Library Usage

```go
package main

import (
    "log"

    "github.com/your-org/migrate/v4"
    _ "github.com/your-org/migrate/v4/database/postgres"
    _ "github.com/your-org/migrate/v4/source/file"
)

func main() {
    m, err := migrate.New(
        "file://./migrations",
        "postgres://localhost:5432/mydb?sslmode=disable",
    )
    if err != nil {
        log.Fatal(err)
    }
    defer m.Close()

    if err := m.Up(); err != nil && err != migrate.ErrNoChange {
        log.Fatal(err)
    }
}
```

## Migration Files

Migration files follow the naming convention:

```
{version}_{title}.up.{extension}
{version}_{title}.down.{extension}
```

Example:
```
000001_create_users_table.up.sql
000001_create_users_table.down.sql
000002_add_email_index.up.sql
000002_add_email_index.down.sql
```

> **Note:** I prefer zero-padded 6-digit version numbers (e.g. `000001`) over plain integers — it keeps directory listings sorted correctly and makes the sequence easier to read at a glance.

## License

MIT
