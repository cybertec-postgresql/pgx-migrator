# pgx-migrator

Simple [pgx](https://github.com/jackc/pgx) oriented [PostgreSQL](https://www.postgresql.org/) schema migration library for Go based on [lopezator/migrator](https://github.com/lopezator/migrator).

# Features

* Simple code
* Usage as a library, embeddable and extensible on your behalf
* Made to use with `jackc/pgx`
* Go code migrations, either transactional or transaction-less, using `pgx.Tx` (`migrator.Migration`) or `pgx.Conn` and `pgx.Pool` (`migrator.MigrationNoTx`)
* No need to use `//go:embed` or others, since all migrations are just Go code

# Usage

Customize this to your needs by changing the driver and/or connection settings.

### QuickStart:

```go
package main

import (

	pgx "github.com/jackc/pgx/v5"
	migrator "github.com/cybertec-postgresql/pgx-migrator"
)

func main() {
    // Configure migrations
    m, err := migrator.New(
        migrator.Migrations(
            &migrator.Migration{
                Name: "Create table foo",
                Func: func(ctx context.Context, tx pgx.Tx) error {
                    _, err := tx.Exec(ctx, "CREATE TABLE foo (id INT PRIMARY KEY)")
                    return err
                },
            },
        ),
    )
    if err != nil {
        panic(err)
    }
   
    // Open database connection
    conn, err := pgx.Connect(context.Background(), os.Getenv("DATABASE_URL"))
    if err != nil {
        panic(err)
    }
    
    // Migrate up
    if err := m.Migrate(conn); err != nil {
        panic(err)
    }
}
```