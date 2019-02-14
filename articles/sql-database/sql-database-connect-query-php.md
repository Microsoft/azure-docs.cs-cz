---
title: Použití PHP k dotazování databáze Azure SQL | Dokumentace Microsoftu
description: Jak pomocí PHP vytvořit program, který se připojí ke službě Azure SQL database a ji dotazovat s použitím příkazů T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: af2e711cef6e97935c2e4dc90557ef4f127d1caa
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237430"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Rychlý start: Použití PHP k dotazování databáze SQL Azure

Tento článek ukazuje, jak používat [PHP](http://php.net/manual/en/intro-whatis.php) pro připojení k databázi Azure SQL. Pak můžete použít příkazy jazyka T-SQL k dotazování dat.

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

- Související s PHP nainstalován software pro váš operační systém:

  - **MacOS**, nainstalujte PHP, ovladač ODBC, potom nainstalujte ovladač PHP pro SQL Server. Zobrazit [kroku 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, nainstalujte PHP, ovladač ODBC, potom nainstalujte ovladač PHP pro SQL Server. Zobrazit [kroku 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, nainstalujte PHP pro službu IIS Express a Chocolatey a potom nainstalujte ovladač ODBC a Nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

Získejte informace o připojení potřebné pro připojení k databázi Azure SQL. Nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte **databází SQL** nebo **spravované instance SQL** stránky.

3. Na **přehled** stránce si prohlédněte plně kvalifikovaný název vedle **název serveru** pro izolované databáze nebo serveru plně kvalifikovaný název vedle **hostitele** pro spravované instance. Zkopírujte název serveru nebo název hostitele, je ukazatel myši a vyberte **kopírování** ikonu.

## <a name="add-code-to-query-database"></a>Přidejte kód pro dotaz na databázi

1. V oblíbeném textovém editoru vytvořte nový soubor *sqltest.php*.  

1. Nahraďte jeho obsah následujícím kódem. Přidejte příslušné hodnoty pro server, databázi, uživatele a heslo.

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

1. Ověření se vrátí prvních 20 řádků a zavřete okno aplikace.

## <a name="next-steps"></a>Další postup

- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)

- [Ovladače Microsoft PHP pro SQL Server](https://github.com/Microsoft/msphpsql/)

- [Hlášení problémů nebo kladení dotazů](https://github.com/Microsoft/msphpsql/issues)

- [Příklad logiky opakování: Odolné připojení k SQL pomocí PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
