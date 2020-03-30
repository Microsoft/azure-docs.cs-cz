---
title: Použití dotazu Přejít
description: Vytvořte pomocí jazyka Go program, který se připojí ke službě Azure SQL Database, a dotazujte a upravujte data s použitím příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: craigg
ms.reviewer: MightyPen
ms.date: 02/12/2019
ms.openlocfilehash: 9b85b1bfb8935b5e311bb7d9503c17261a210127
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827080"
---
# <a name="quickstart-use-golang-to-query-an-azure-sql-database"></a>Úvodní příručka: Použití Golangu k dotazování na databázi Azure SQL

V tomto rychlém startu použijete programovací jazyk [Golang](https://godoc.org/github.com/denisenkom/go-mssqldb) pro připojení k databázi Azure SQL. Potom spustíte příkazy Transact-SQL pro dotazování a úpravy dat. [Golang](https://golang.org/) je open-source programovací jazyk, který usnadňuje vytváření jednoduchého, spolehlivého a efektivního softwaru.  

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Databázi Azure SQL. Pomocí jednoho z těchto rychlých startů můžete vytvořit a pak nakonfigurovat databázi v Azure SQL Database:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvořit| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Cli](scripts/sql-database-create-and-configure-database-cli.md) | [Cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall IP na úrovni serveru](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního virtuálního zařízení](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z místa na místě](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Adventure Works načtený podle rychlého startu|[Obnovit wide world dovozci](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo import adventure works ze souboru [BACPAC](sql-database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou zapsány k použití databáze Adventure Works. Se spravovanou instancí je nutné buď importovat databázi adventure works do databáze instancí, nebo upravit skripty v tomto článku, aby bylo nutné používat databázi Wide World Importers.

- Golang a související software pro váš operační systém nainstalován:

  - **MacOS**: Nainstalujte Homebrew a Golang. Viz [krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
  - **Ubuntu**: Nainstalujte Golang. Viz [krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
  - **Windows**: Instalace Golang. Viz [krok 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.**

## <a name="create-golang-project-and-dependencies"></a>Vytvoření projektu Golang a závislostí

1. Z terminálu vytvořte složku nového projektu s názvem **SqlServerSample**. 

   ```bash
   mkdir SqlServerSample
   ```

2. Přejděte na **sqlserversample** a nainstalujte ovladač SQL Server pro Go.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Vytvoření ukázkových dat

1. V textovém editoru vytvořte soubor s názvem **CreateTestData.sql** ve složce **SqlServerSample.** Do souboru vložte tento kód T-SQL, který vytvoří schéma, tabulku a vloží několik řádků.

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

2. Slouží `sqlcmd` k připojení k databázi a spuštění nově vytvořeného skriptu SQL. Hodnoty pro server, databázi, uživatelské jméno a heslo nahraďte příslušnými hodnotami.

   ```bash
   sqlcmd -S <your_server>.database.windows.net -U <your_username> -P <your_password> -d <your_database> -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Vložení kódu pro dotazování databáze SQL

1. Ve složce **SqlServerSample** vytvořte soubor **sample.go**.

2. Do souboru vložte tento kód. Přidejte hodnoty pro server, databázi, uživatelské jméno a heslo. Tento příklad používá [kontextové metody](https://golang.org/pkg/context/) Golang k ujistěte se, že je aktivní připojení databázového serveru.

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

   var server = "<your_server.database.windows.net>"
   var port = 1433
   var user = "<your_username>"
   var password = "<your_password>"
   var database = "<your_database>"

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

1. Na příkazovém řádku spusťte následující příkaz.

   ```bash
   go run sample.go
   ```

2. Ověřte výstup.

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

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)
- [Ovladač Golang pro Microsoft SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Hlášení problémů nebo kladení dotazů](https://github.com/denisenkom/go-mssqldb/issues)

