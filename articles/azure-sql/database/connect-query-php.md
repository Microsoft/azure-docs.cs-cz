---
title: Použít PHP k dotazování
description: Jak pomocí PHP vytvořit program, který se připojí k databázi v Azure SQL Database a provede dotaz pomocí příkazů T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.custom: sqldbrb=2 
ms.openlocfilehash: d3c064b590e5e3c49f8c42e913467596080a0887
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054465"
---
# <a name="quickstart-use-php-to-query-a-microsoft-azure-sql-database"></a>Rychlý Start: použití PHP k dotazování na Microsoft Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek ukazuje, jak použít [php](https://php.net/manual/en/intro-whatis.php) pro připojení k databázi SQL Azure. Pak můžete použít příkazy T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL můžete použít jeden z těchto rychlých startů:

  || SQL Database | Spravovaná instance SQL | SQL Server na virtuálním počítači Azure |
  |:--- |:--- |:---|:---|
  | Vytvořit| [Azure Portal](single-database-create-quickstart.md) | [Azure Portal](../managed-instance/instance-create-quickstart.md) | [Azure Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Rozhraní příkazového řádku](scripts/create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](firewall-create-server-level-portal-quickstart.md)| [Připojení z virtuálního počítače](../managed-instance/connect-vm-instance-configure.md)|
  |||[Připojení z webu](../managed-instance/point-to-site-p2s-configure.md) | [Připojení k SQL Serveru](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) | [Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) |
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||




  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Pomocí spravované instance SQL je nutné buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.

- Nainstalovaný software týkající se PHP pro váš operační systém:

  - **MacOS**, nainstalujte php, ovladač ODBC a pak nainstalujte ovladač php pro SQL Server. Viz [Krok 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, nainstalujte php, ovladač ODBC a pak nainstalujte ovladač PHP pro SQL Server. Viz [Krok 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, nainstalujte si PHP pro IIS Express a čokoláda a pak nainstalujte ovladač ODBC a Nástroj Sqlcmd. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** Azure SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** spravované instance Azure SQL nebo SQL Server ve virtuálním počítači Azure. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

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

- [Návrh prvního Azure SQL Database](design-first-database-tutorial.md)

- [Ovladače Microsoft PHP pro SQL Server](https://github.com/Microsoft/msphpsql/)

- [Hlášení problémů nebo kladení dotazů](https://github.com/Microsoft/msphpsql/issues)

- [Příklad logiky opakování: Odolné připojení k SQL pomocí PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
