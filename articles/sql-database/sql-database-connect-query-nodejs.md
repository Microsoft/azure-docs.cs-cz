---
title: Použití Node.js k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: Jak pomocí Node.js vytvořit program, který se připojí ke službě Azure SQL database a ji dotazovat s použitím příkazů T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/26/2018
ms.openlocfilehash: 3a6060c59c2b338a2fad3327fe89dcf3df955ef5
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496693"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Rychlý start: Použití Node.js k dotazování databáze SQL Azure

Tento článek ukazuje, jak používat [Node.js](https://nodejs.org) pro připojení k databázi Azure SQL. Pak můžete použít příkazy jazyka T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

K dokončení této ukázce, ujistěte se, že jsou splněné následující požadavky:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A [pravidlo brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md) pro veřejnou IP adresu počítače, které používáte

- Node.js související software pro váš operační systém:

  - **MacOS**, nainstalujte Homebrew a Node.js a potom nainstalujte ovladač ODBC a Nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**, nainstalujte Node.js a potom nainstalujte ovladač ODBC a Nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**, nainstalujte Chocolatey a Node.js a potom nainstalujte ovladač ODBC a Nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-database-connection"></a>Získání připojení k databázi

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Musíte mít nastavené pravidlo brány firewall pro veřejnou IP adresu počítače, na kterém provádíte tento kurz. Pokud jste na jiném počítači nebo máte jinou veřejnou IP adresu, vytvořte [pravidlo brány firewall na úrovni serveru pomocí webu Azure portal](sql-database-get-started-portal-firewall.md).

## <a name="create-the-project"></a>Vytvoření projektu

Otevřete příkazový řádek a vytvořte složku *sqltest*. Přejděte do složky, kterou jste vytvořili, a spusťte následující příkaz:

  ```bash
  npm init -y
  npm install tedious
  npm install async
  ```

## <a name="add-code-to-query-database"></a>Přidejte kód pro dotaz na databázi

1. Ve svém oblíbeném textovém editoru vytvořte nový soubor, *sqltest.js*.

1. Nahraďte jeho obsah následujícím kódem. Přidejte příslušné hodnoty pro server, databázi, uživatele a heslo.

    ```js
    var Connection = require('tedious').Connection;
    var Request = require('tedious').Request;

    // Create connection to database
    var config =
    {
        userName: 'your_username', // update me
        password: 'your_password', // update me
        server: 'your_server.database.windows.net', // update me
        options:
        {
            database: 'your_database', //update me
            encrypt: true
        }
    }
    var connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on('connect', function(err)
        {
            if (err)
            {
                console.log(err)
            }
            else
            {
                queryDatabase()
            }
        }
    );

    function queryDatabase()
    {
        console.log('Reading rows from the Table...');

        // Read all rows from table
        request = new Request(
            "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc "
                + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
            function(err, rowCount, rows)
            {
                console.log(rowCount + ' row(s) returned');
                process.exit();
            }
        );

        request.on('row', function(columns) {
            columns.forEach(function(column) {
                console.log("%s\t%s", column.metadata.colName, column.value);
            });
        });
        connection.execSql(request);
    }
    ```

> [!NOTE]
> Příklad kódu používá **AdventureWorksLT** ukázkovou databázi SQL Azure.

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte program.

    ```bash
    node sqltest.js
    ```

1. Ověření se vrátí prvních 20 řádků a zavřete okno aplikace.

## <a name="next-steps"></a>Další postup

- [Ovladač Microsoft Node.js pro SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Připojení a dotazování na Windows, Linux nebo macOS s [.NET core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md), nebo [SSMS](sql-database-connect-query-ssms.md) (jenom Windows)

- [Začínáme s .NET Core ve Windows, Linux nebo macOS pomocí příkazového řádku](/dotnet/core/tutorials/using-with-xplat-cli)

- Navrhnout vaši první Azure SQL database pomocí [.NET](sql-database-design-first-database-csharp.md) nebo [aplikace SSMS](sql-database-design-first-database.md)
