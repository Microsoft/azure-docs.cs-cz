---
title: Použití jazyka Go k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: Vytvořte pomocí jazyka Go program, který se připojí ke službě Azure SQL Database, a dotazujte a upravujte data s použitím příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: v-daveng
ms.reviewer: MightyPen
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: c270fef40b732f170add32ef52eeadc790d8cd83
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913497"
---
# <a name="quickstart-use-go-to-query-an-azure-sql-database"></a>Rychlý start: Použití jazyka Go k dotazování databáze SQL Azure

Tento rychlý start ukazuje, jak se pomocí jazyka [Go](https://godoc.org/github.com/denisenkom/go-mssqldb) připojit k databázi SQL Azure. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Pravidlo brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md) pro veřejnou IP adresu počítače, který používáte pro tento rychlý start.

- Máte nainstalovaný jazyk Go a související software pro váš operační systém:

    - **MacOS:** Nainstalujte Homebrew a jazyk Go. Viz [krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
    - **Ubuntu:** Nainstalujte jazyk Go. Viz [krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
    - **Windows:** Nainstalujte jazyk Go. Viz [krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).    

## <a name="sql-server-connection-information"></a>Informace o připojení k SQL serveru

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-go-project-and-dependencies"></a>Vytvoření projektu a závislostí v jazyce Go

1. Z terminálu vytvořte složku nového projektu s názvem **SqlServerSample**. 

   ```bash
   mkdir SqlServerSample
   ```

2. Přejděte do adresáře **SqlServerSample** a získejte a nainstalujte ovladač SQL Serveru pro jazyk Go:

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Vytvoření ukázkových dat

1. Pomocí oblíbeného textového editoru vytvořte ve složce **SqlServerSample** soubor **CreateTestData.sql**. Zkopírujte a vložte do něj následující kód T-SQL. Tento kód vytvoří schéma a tabulku a vloží několik řádků.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. Pomocí sqlcmd se připojte k databázi a spuštěním skriptu SQL vytvořte schéma a tabulku a vložte několik řádků. Hodnoty pro server, databázi, uživatelské jméno a heslo nahraďte příslušnými hodnotami.

   ```bash
   sqlcmd -S your_server.database.windows.net -U your_username -P your_password -d your_database -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Vložení kódu pro dotazování databáze SQL

1. Ve složce **SqlServerSample** vytvořte soubor **sample.go**.

2. Soubor otevřete a nahraďte jeho obsah následujícím kódem. Přidejte příslušné hodnoty pro váš server, databázi, uživatelské jméno a heslo. Tento příklad k zajištění aktivního připojení k databázovému serveru používá kontextové metody jazyka Go.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
       "errors"
   )

   var db *sql.DB

   var server = "your_server.database.windows.net"
   var port = 1433
   var user = "your_username"
   var password = "your_password"
   var database = "your_database"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool: ", err.Error())
       }
       ctx := context.Background()
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal(err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createID, err := CreateEmployee("Jake", "United States")
       if err != nil {
           log.Fatal("Error creating Employee: ", err.Error())
       }
       fmt.Printf("Inserted ID: %d successfully.\n", createID)

       // Read employees
       count, err := ReadEmployees()
       if err != nil {
           log.Fatal("Error reading Employees: ", err.Error())
       }
       fmt.Printf("Read %d row(s) successfully.\n", count)

       // Update from database
       updatedRows, err := UpdateEmployee("Jake", "Poland")
       if err != nil {
           log.Fatal("Error updating Employee: ", err.Error())
       }
       fmt.Printf("Updated %d row(s) successfully.\n", updatedRows)

       // Delete from database
       deletedRows, err := DeleteEmployee("Jake")
       if err != nil {
           log.Fatal("Error deleting Employee: ", err.Error())
       }
       fmt.Printf("Deleted %d row(s) successfully.\n", deletedRows)
   }

   // CreateEmployee inserts an employee record
   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           err = errors.New("CreateEmployee: db is null")
           return -1, err
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := "INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location); select convert(bigint, SCOPE_IDENTITY());"

       stmt, err := db.Prepare(tsql)
       if err != nil {
          return -1, err
       }
       defer stmt.Close()

       row := stmt.QueryRowContext(
           ctx,
           sql.Named("Name", name),
           sql.Named("Location", location))
       var newID int64
       err = row.Scan(&newID)
       if err != nil {
           return -1, err
       }

       return newID, nil
   }

   // ReadEmployees reads all employee records
   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           return -1, err
       }

       defer rows.Close()

       var count int

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // UpdateEmployee updates an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name = @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }

   // DeleteEmployee deletes an employee from the database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name = @Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>Spuštění kódu

1. V příkazovém řádku spusťte následující příkazy:

   ```bash
   go run sample.go
   ```

2. Ověřte výstup:

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 row(s) successfully.
   Updated 1 row(s) successfully.
   Deleted 1 row(s) successfully.
   ```

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)
- [Ovladač Go pro Microsoft SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Hlášení problémů nebo kladení dotazů](https://github.com/denisenkom/go-mssqldb/issues)

