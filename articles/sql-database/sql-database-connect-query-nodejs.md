---
title: Použití Node. js k dotazování databáze
description: Jak pomocí Node. js vytvořit program, který se připojí ke službě Azure SQL Database a provede dotaz pomocí příkazů T-SQL.
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
ms.openlocfilehash: d22d95a6c4f417f803793d0c87ee251f7f0e9ed5
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758423"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Rychlý start: Použití Node.js k dotazování databáze Azure SQL

V tomto rychlém startu použijete Node. js k připojení k databázi SQL Azure a k dotazování na data použijete příkazy T-SQL.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [Databáze SQL Azure](sql-database-single-database-get-started.md)
  
- Software související s [Node. js](https://nodejs.org)

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Nainstalujte homebrew a Node. js a potom nainstalujte ovladač ODBC a SQLCMD pomocí kroků **1,2** a **1,3** v části [vytváření aplikací Node. js pomocí SQL Server v MacOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Nainstalujte Node. js a potom nainstalujte ovladač ODBC a SQLCMD pomocí kroků **1,2** a **1,3** v části [vytváření aplikací Node. js pomocí SQL Server v Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Nainstalujte čokolády a Node. js a potom nainstalujte ovladač ODBC a SQLCMD pomocí kroků **1,2** a **1,3** v části [vytváření aplikací Node. js pomocí SQL Server ve Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Skripty v tomto článku jsou určeny k používání databáze **Adventure Works** .

> [!NOTE]
> Volitelně můžete zvolit použití spravované instance Azure SQL.
>
> K vytvoření a konfiguraci použijte [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)nebo [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)a pak nastavte připojení [na pracovišti](sql-database-managed-instance-configure-p2s.md) nebo [virtuálním počítači](sql-database-managed-instance-configure-vm.md) .
>
> Pokud chcete načíst data, přečtěte si téma [Restore with BacPac](sql-database-import.md) se souborem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) nebo si přečtěte část [obnovení databáze World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [Portálu Azure](https://portal.azure.com/).

2. Přejít na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** . 

## <a name="create-the-project"></a>Vytvoření projektu

Otevřete příkazový řádek a vytvořte složku *sqltest*. Otevřete složku, kterou jste vytvořili, a spusťte následující příkaz:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Přidání kódu do databáze dotazů

1. V oblíbeném textovém editoru vytvořte nový soubor *sqltest. js*.

1. Nahraďte jeho obsah následujícím kódem. Pak přidejte příslušné hodnoty pro váš server, databázi, uživatele a heslo.

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

1. Ověřte, že se vrátí prvních 20 řádků a okno aplikace zavřete.

## <a name="next-steps"></a>Další kroky

- [Ovladač Microsoft Node.js pro SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Připojení a dotazování na Windows/Linux/macOS pomocí [.NET Core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md)nebo [SSMS](sql-database-connect-query-ssms.md) (jenom Windows)

- [Začínáme s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)

- Návrh první databáze SQL Azure pomocí [rozhraní .NET](sql-database-design-first-database-csharp.md) nebo [SSMS](sql-database-design-first-database.md)
