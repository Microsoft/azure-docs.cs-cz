---
title: Použití souboru Node.js k dotazování na databázi
description: Jak pomocí souboru Node.js vytvořit program, který se připojí k databázi Azure SQL, a zadat dotaz pomocí příkazů T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768603"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Rychlý start: Použití Node.js k dotazování databáze Azure SQL

V tomto rychlém startu použijete node.js pro připojení k databázi Azure SQL a pomocí příkazů T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze Azure SQL](sql-database-single-database-get-started.md)
- Software související s [node.js](https://nodejs.org)

  # <a name="macos"></a>[Macos](#tab/macos)

  Nainstalujte soubor Homebrew a Node.js a potom nainstalujte ovladač ODBC a SQLCMD pomocí kroků **1.2** a **1.3** v [aplikacích Create Node.js pomocí serveru SQL Server v systému macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Nainstalujte soubor Node.js a nainstalujte ovladač ODBC a SQLCMD pomocí kroků **1.2** a **1.3** v [aplikacích Create Node.js pomocí serveru SQL Server v ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Nainstalujte soubor Chocolatey a Node.js a potom nainstalujte ovladač ODBC a SQLCMD pomocí kroků **1.2** a **1.3** v [aplikacích Create Node.js pomocí serveru SQL Server v systému Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Skripty v tomto článku jsou zapsány k použití databáze **Adventure Works.**

> [!NOTE]
> Volitelně můžete zvolit použití spravované instance Azure SQL.
>
> Chcete-li vytvořit a nakonfigurovat, použijte [portál Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)nebo [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)a nastavte připojení [na místě](sql-database-managed-instance-configure-p2s.md) nebo [v počítači.](sql-database-managed-instance-configure-vm.md)
>
> Chcete-li načíst data, viz [obnovení pomocí BACPAC](sql-database-import.md) se souborem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) nebo naleznete [v tématu obnovení databáze Wide World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.** 

## <a name="create-the-project"></a>Vytvoření projektu

Otevřete příkazový řádek a vytvořte složku *sqltest*. Otevřete složku, kterou jste vytvořili, a spusťte následující příkaz:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Přidání kódu do databáze dotazů

1. Ve svém oblíbeném textovém editoru vytvořte nový soubor *sqltest.js*.

1. Nahraďte jeho obsah následujícím kódem. Potom přidejte příslušné hodnoty pro váš server, databázi, uživatele a heslo.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> Příklad kódu používá ukázkovou databázi **AdventureWorksLT** pro Azure SQL.

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte program.

    ```bash
    node sqltest.js
    ```

1. Ověřte, zda je vráceno prvních 20 řádků a zavřete okno aplikace.

## <a name="next-steps"></a>Další kroky

- [Ovladač Microsoft Node.js pro SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Připojení a dotazování na Windows/Linux/macOS s [jádrem .NET](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md)nebo [SSMS](sql-database-connect-query-ssms.md) (jenom Windows)

- [Začínáme s rozhraním .NET Core ve Windows/Linux/macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)

- Návrh první databáze Azure SQL pomocí [rozhraní .NET](sql-database-design-first-database-csharp.md) nebo [SSMS](sql-database-design-first-database.md)
