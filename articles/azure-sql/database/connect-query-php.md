---
title: Použít PHP k dotazování
description: Jak pomocí PHP vytvořit program, který se připojí k databázi v Azure SQL Database nebo Azure SQL Managed instance, a Dotazujte ji pomocí příkazů T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2
ms.openlocfilehash: e0ad8e9e71a0ee2c23412d535fe79955edb39ba8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576646"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>Rychlý Start: použití PHP k dotazování databáze v Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Tento článek ukazuje, jak použít [php](https://php.net/manual/en/intro-whatis.php) pro připojení k databázi v Azure SQL Database nebo v Azure SQL Managed instance. Pak můžete použít příkazy T-SQL k dotazování dat.

## <a name="prerequisites"></a>Předpoklady

Co budete potřebovat k dokončení tohoto rychlého startu:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Databáze v Azure SQL Database nebo spravované instanci Azure SQL. K vytvoření a konfiguraci databáze můžete použít jeden z těchto rychlých startů:

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




  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Pomocí spravované instance SQL je nutné buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

- Nainstalovaný software týkající se PHP pro váš operační systém:

  - **MacOS**, nainstalujte php, ovladač ODBC a pak nainstalujte ovladač php pro SQL Server. Viz [Krok 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, nainstalujte php, ovladač ODBC a pak nainstalujte ovladač PHP pro SQL Server. Viz [Krok 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, nainstalujte ovladače PHP a PHP a potom nainstalujte ovladač ODBC a Nástroj Sqlcmd. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-server-connection-information"></a>Získat informace o připojení k serveru

Získejte informace o připojení, které potřebujete pro připojení k databázi v Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL**  nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro databázi v Azure SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** spravované instance Azure SQL nebo SQL Server ve virtuálním počítači Azure. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k instanci SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="add-code-to-query-the-database"></a>Přidání kódu pro dotaz do databáze

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

- [Návrh první databáze v Azure SQL Database](design-first-database-tutorial.md)
- [Ovladače Microsoft PHP pro SQL Server](https://github.com/Microsoft/msphpsql/)
- [Hlášení problémů nebo kladení dotazů](https://github.com/Microsoft/msphpsql/issues)
- [Příklad logiky opakování: odolné připojení k Azure SQL pomocí PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
