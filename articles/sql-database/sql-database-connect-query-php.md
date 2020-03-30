---
title: Použití PHP k dotazování
description: Jak pomocí PHP vytvořit program, který se připojí k databázi Azure SQL a dotaz ovat pomocí příkazů T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.openlocfilehash: ae119dd23da670f16c0239b14119519c431e6326
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827014"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Rychlý start: Použití PHP k dotazování databáze Azure SQL

Tento článek ukazuje, jak používat [PHP](https://php.net/manual/en/intro-whatis.php) pro připojení k databázi Azure SQL. Potom můžete použít T-SQL příkazy k dotazování dat.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tuto ukázku, ujistěte se, že máte následující požadavky:

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

- Software související s PHP nainstalovaný pro váš operační systém:

  - **MacOS**, nainstalujte PHP, ovladač ODBC, pak nainstalujte ovladač PHP pro SQL Server. Viz [krok 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, nainstalujte PHP, ovladač ODBC, pak nainstalujte ovladač PHP pro SQL Server. Viz [krok 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, nainstalujte PHP pro Služby IIS Express a Chocolatey, potom nainstalujte ovladač ODBC a SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.**

## <a name="add-code-to-query-database"></a>Přidání kódu do databáze dotazů

1. V oblíbeném textovém editoru vytvořte nový soubor *sqltest.php*.  

1. Nahraďte jeho obsah následujícím kódem. Potom přidejte příslušné hodnoty pro váš server, databázi, uživatele a heslo.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte aplikaci.

   ```bash
   php sqltest.php
   ```

1. Ověřte, zda je vráceno prvních 20 řádků a zavřete okno aplikace.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)

- [Ovladače Microsoft PHP pro SQL Server](https://github.com/Microsoft/msphpsql/)

- [Hlášení problémů nebo kladení dotazů](https://github.com/Microsoft/msphpsql/issues)

- [Příklad logiky opakování: Odolné připojení k SQL pomocí PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
