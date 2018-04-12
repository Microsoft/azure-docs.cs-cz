---
title: Použití jazyka Go k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: Vytvořte pomocí jazyka Go program, který se připojí ke službě Azure SQL Database, a dotazujte a upravujte data s použitím příkazů jazyka Transact-SQL.
services: sql-database
author: David-Engel
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: go
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: v-daveng
ms.openlocfilehash: b10d3e469ffac22a2b0994708f50125d21496fef
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-go-to-query-an-azure-sql-database"></a>Použití jazyka Go k dotazování databáze SQL Azure

Tento rychlý úvodní kurz ukazuje použití jazyka [Go](https://godoc.org/github.com/denisenkom/go-mssqldb) k připojení k databázi SQL Azure. Ukazuje se také použití příkazů jazyka Transact-SQL k dotazování a úpravě dat.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý úvodní kurz, ujistěte se, že máte následující:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Pravidlo brány firewall na úrovni serveru](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pro veřejnou IP adresu počítače, který používáte pro tento rychlý úvodní kurz.

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
           log.Fatal("Error creating connection pool:", err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createId, err := CreateEmployee("Jake", "United States")
       fmt.Printf("Inserted ID: %d successfully.\n", createId)

       // Read employees
       count, err := ReadEmployees()
       fmt.Printf("Read %d rows successfully.\n", count)

       // Update from database
       updateId, err := UpdateEmployee("Jake", "Poland")
       fmt.Printf("Updated row with ID: %d successfully.\n", updateId)

       // Delete from database
       rows, err := DeleteEmployee("Jake")
       fmt.Printf("Deleted %d rows successfully.\n", rows)
   }

   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           log.Fatal("What?")
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name,@Location);")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))

       if err != nil {
           log.Fatal("Error inserting new row: " + err.Error())
           return -1, err
       }

       return result.LastInsertId()
   }

   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           log.Fatal("Error reading rows: " + err.Error())
           return -1, err
       }

       defer rows.Close()

       var count int = 0

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               log.Fatal("Error reading rows: " + err.Error())
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // Update an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name= @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           log.Fatal("Error updating row: " + err.Error())
           return -1, err
       }

       return result.LastInsertId()
   }

   // Delete an employee from database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name=@Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           fmt.Println("Error deleting row: " + err.Error())
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
   Read 4 rows successfully.
   Updated row with ID: 4 successfully.
   Deleted 1 rows successfully.
   ```

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)
- [Ovladač Go pro Microsoft SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Hlášení problémů nebo kladení dotazů](https://github.com/denisenkom/go-mssqldb/issues)

