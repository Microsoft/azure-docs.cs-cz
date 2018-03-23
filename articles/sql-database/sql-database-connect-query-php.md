---
title: Použití PHP k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: Toto téma vám ukáže, jak pomocí PHP vytvořit program, který se připojí ke službě Azure SQL Database a bude ji dotazovat s použitím příkazů jazyka Transact-SQL.
services: sql-database
author: CarlRabeler
manager: jhubbard
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: php
ms.topic: quickstart
ms.date: 11/29/2017
ms.author: carlrab
ms.openlocfilehash: 7f6877bff317ac52b2709c9c71dc37a3f820fe20
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Použití PHP k dotazování databáze SQL Azure

Tento rychlý úvodní kurz ukazuje použití [PHP](http://php.net/manual/en/intro-whatis.php) k vytvoření programu pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý úvodní kurz, ujistěte se, že máte následující:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Pravidlo brány firewall na úrovni serveru](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pro veřejnou IP adresu počítače, který používáte pro tento rychlý úvodní kurz.

- Máte nainstalovaný jazyk PHP a související software pro váš operační systém:

    - **MacOS:** Nainstalujte Homebrew a PHP, nainstalujte ovladač ODBC a nástroj SQLCMD a potom nainstalujte ovladač PHP pro SQL Server. Viz [kroky 1.2, 1.3 a 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).
    - **Ubuntu:** Nainstalujte PHP a další požadované balíčky a potom nainstalujte ovladač PHP pro SQL Server. Viz [kroky 1.2 a 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows:** Nainstalujte nejnovější verzi PHP pro službu IIS Express, nejnovější verzi ovladačů Microsoft pro SQL Server ve službě IIS Express, Chocolatey, ovladač ODBC a nástroj SQLCMD. Viz [kroky 1.2 a 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Informace o připojení k SQL serveru

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Vložení kódu pro dotazování databáze SQL

1. V oblíbeném textovém editoru vytvořte nový soubor **sqltest.php**.  

2. Nahraďte jeho obsah následujícím kódem a přidejte odpovídající hodnoty pro váš server, databázi, uživatele a heslo.

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
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

1. V příkazovém řádku spusťte následující příkazy:

   ```php
   php sqltest.php
   ```

2. Ověřte, že se vrátilo prvních 20 řádků, a potom zavřete okno aplikace.

## <a name="next-steps"></a>Další kroky
- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)
- [Ovladače Microsoft PHP pro SQL Server](https://github.com/Microsoft/msphpsql/)
- [Hlášení problémů nebo kladení dotazů](https://github.com/Microsoft/msphpsql/issues)
- [Příklad logiky opakování: Odolné připojení k SQL pomocí PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

