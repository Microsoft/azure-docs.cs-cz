---
title: Použití Node.js k dotazování databáze
description: Jak pomocí Node.js vytvořit program, který se připojuje k databázi v Azure SQL Database nebo spravované instanci SQL Azure a dotazuje se pomocí příkazů T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-js
ms.openlocfilehash: 44530577972839aacb803d1722fa97716088fa0d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325432"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Rychlý Start: použití Node.js k dotazování databáze v Azure SQL Database nebo spravované instanci SQL Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V tomto rychlém startu použijete Node.js pro připojení k databázi a dotazování na data.

## <a name="prerequisites"></a>Požadavky

Co budete potřebovat k dokončení tohoto rychlého startu:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  | Akce | Databáze SQL | Spravovaná instance SQL | SQL Server na virtuálním počítači Azure |
  |:--- |:--- |:---|:---|
  | Vytvořit| [Azure Portal](single-database-create-quickstart.md) | [Azure Portal](../managed-instance/instance-create-quickstart.md) | [Azure Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Rozhraní příkazového řádku](scripts/create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](firewall-create-server-level-portal-quickstart.md)| [Připojení z virtuálního počítače](../managed-instance/connect-vm-instance-configure.md)|
  |||[Připojení z místního prostředí](../managed-instance/point-to-site-p2s-configure.md) | [Připojení k instanci SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) | [Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) |
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||


- Software související s [Node.js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Nainstalujte homebrew a Node.js a potom nainstalujte ovladač ODBC a SQLCMD pomocí kroků **1,2** a **1,3** v části [Vytvoření Node.js aplikací pomocí SQL Server na MacOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Nainstalujte Node.js a potom nainstalujte ovladač ODBC a nástroj SQLCMD pomocí kroků **1,2** a **1,3** v části [vytváření Node.jsch aplikací pomocí SQL Server na Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Nainstalujte čokolády a Node.js a potom nainstalujte ovladač ODBC a nástroj SQLCMD pomocí kroků **1,2** a **1,3** při [vytváření aplikací Node.js pomocí SQL Server ve Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Skripty v tomto článku jsou určeny k používání databáze **Adventure Works** .

> [!NOTE]
> Volitelně můžete zvolit použití spravované instance Azure SQL.
>
> K vytvoření a konfiguraci použijte [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)nebo [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)a pak nastavte [místní nebo](../managed-instance/point-to-site-p2s-configure.md) konektivitu [virtuálních počítačů](../managed-instance/connect-vm-instance-configure.md) .
>
> Pokud chcete načíst data, přečtěte si téma [Restore with BacPac](database-import.md) se souborem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) nebo si přečtěte část [obnovení databáze World Importers](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-server-connection-information"></a>Získat informace o připojení k serveru

Získejte informace o připojení, které potřebujete pro připojení k databázi v Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejít na stránku **databáze SQL**  nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro databázi v Azure SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** spravované instance Azure SQL nebo SQL Server na virtuálním počítači Azure. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-the-project"></a>Vytvoření projektu

Otevřete příkazový řádek a vytvořte složku *sqltest*. Otevřete složku, kterou jste vytvořili, a spusťte následující příkaz:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>Přidání kódu pro dotaz do databáze

1. V oblíbeném textovém editoru vytvořte nový soubor *sqltest.js*.

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
> Příklad kódu používá ukázkovou databázi **AdventureWorksLT** v Azure SQL Database.

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte program.

    ```bash
    node sqltest.js
    ```

1. Ověřte, že se vrátí prvních 20 řádků a okno aplikace zavřete.

## <a name="next-steps"></a>Další kroky

- [Ovladač Microsoft Node.js pro SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Připojení a dotazování na Windows/Linux/macOS pomocí [.NET Core](connect-query-dotnet-core.md), [Visual Studio Code](connect-query-vscode.md)nebo [SSMS](connect-query-ssms.md) (jenom Windows)

- [Začínáme s .NET Core v systému Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)

- Návrh první databáze v Azure SQL Database pomocí [rozhraní .NET](design-first-database-csharp-tutorial.md) nebo [SSMS](design-first-database-tutorial.md)
