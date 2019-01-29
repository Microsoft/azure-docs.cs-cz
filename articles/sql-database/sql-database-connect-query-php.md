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
ms.date: 11/28/2018
ms.openlocfilehash: b3fe6e0249143b27cb763401a8d328922ed1fe99
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173917"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Rychlý start: Použití PHP k dotazování databáze SQL Azure

Tento článek ukazuje, jak používat [PHP](http://php.net/manual/en/intro-whatis.php) pro připojení k databázi Azure SQL. Pak můžete použít příkazy jazyka T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

K dokončení této ukázce, ujistěte se, že jsou splněné následující požadavky:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Související s PHP nainstalován software pro váš operační systém:

    - **MacOS**, nainstalujte PHP, ovladač ODBC, potom nainstalujte ovladač PHP pro SQL Server. Zobrazit [kroku 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Linux**, nainstalujte PHP, ovladač ODBC, potom nainstalujte ovladač PHP pro SQL Server. Zobrazit [kroku 1, 2 a 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Windows**, nainstalujte PHP pro službu IIS Express a Chocolatey a potom nainstalujte ovladač ODBC a Nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-database-connection"></a>Získání připojení k databázi

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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
