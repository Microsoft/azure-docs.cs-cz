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
ms.date: 02/12/2019
ms.openlocfilehash: 49fe9f51026c4cb096fd8248b53d2e5b5b574923
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236018"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Rychlý start: Použití Node.js k dotazování databáze SQL Azure

Tento článek ukazuje, jak používat [Node.js](https://nodejs.org) pro připojení k databázi Azure SQL. Pak můžete použít příkazy jazyka T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

K dokončení této ukázce, ujistěte se, že jsou splněné následující požadavky:

- Databázi SQL Azure. Jeden z těchto rychlých startech můžete vytvořit a potom nakonfigurovat databázi ve službě Azure SQL Database:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvořit| [Azure Portal](sql-database-single-database-get-started.md) | [Azure Portal](sql-database-managed-instance-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Konfigurace | [pravidlo brány firewall na úrovni serveru IP](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního počítače](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z na místě](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Společnosti Adventure Works načtených za rychlý start|[Obnovit Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo importovat společnosti Adventure Works z [BACPAC](sql-database-import.md) souboru z [githubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku se zapisují do použít databázi společnosti Adventure Works. S managed instance musíte importovat databázi společnosti Adventure Works do instance databáze nebo upravovat skripty v tomto článku pro používání databáze Wide World Importers.


- Node.js související software pro váš operační systém:

  - **MacOS**, nainstalujte Homebrew a Node.js a potom nainstalujte ovladač ODBC a Nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
  
  - **Ubuntu**, nainstalujte Node.js a potom nainstalujte ovladač ODBC a Nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
  
  - **Windows**, nainstalujte Chocolatey a Node.js a potom nainstalujte ovladač ODBC a Nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

Získejte informace o připojení potřebné pro připojení k databázi Azure SQL. Nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte **databází SQL** nebo **spravované instance SQL** stránky.

3. Na **přehled** stránce si prohlédněte plně kvalifikovaný název vedle **název serveru** pro izolované databáze nebo serveru plně kvalifikovaný název vedle **hostitele** pro spravované instance. Zkopírujte název serveru nebo název hostitele, je ukazatel myši a vyberte **kopírování** ikonu. 

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
        var request = new Request(
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
