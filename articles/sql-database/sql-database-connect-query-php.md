---
title: Použít PHP k dotazování
description: Jak pomocí PHP vytvořit program, který se připojí ke službě Azure SQL Database a provede dotaz pomocí příkazů T-SQL.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827014"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Rychlý start: Použití PHP k dotazování databáze Azure SQL

Tento článek ukazuje, jak použít [php](https://php.net/manual/en/intro-whatis.php) pro připojení k databázi SQL Azure. Pak můžete použít příkazy T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

K dokončení této ukázky se ujistěte, že máte následující požadavky:

- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvoření| [Azure Portal](sql-database-single-database-get-started.md) | [Azure Portal](sql-database-managed-instance-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního počítače](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z webu](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](sql-database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Se spravovanou instancí musíte buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

- Nainstalovaný software týkající se PHP pro váš operační systém:

  - **MacOS**, nainstalujte php, ovladač ODBC a pak nainstalujte ovladač php pro SQL Server. Viz [Krok 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, nainstalujte php, ovladač ODBC a pak nainstalujte ovladač PHP pro SQL Server. Viz [Krok 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, nainstalujte si PHP pro IIS Express a čokoláda a pak nainstalujte ovladač ODBC a Nástroj Sqlcmd. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="add-code-to-query-database"></a>Přidání kódu do databáze dotazů

1. V oblíbeném textovém editoru vytvořte nový soubor *sqltest.php*.  

1. Nahraďte jeho obsah následujícím kódem. Pak přidejte příslušné hodnoty pro váš server, databázi, uživatele a heslo.

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

1. Ověřte, že se vrátí prvních 20 řádků a okno aplikace zavřete.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)

- [Ovladače Microsoft PHP pro SQL Server](https://github.com/Microsoft/msphpsql/)

- [Hlášení problémů nebo kladení dotazů](https://github.com/Microsoft/msphpsql/issues)

- [Příklad logiky opakování: odolné připojení k SQL pomocí PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
