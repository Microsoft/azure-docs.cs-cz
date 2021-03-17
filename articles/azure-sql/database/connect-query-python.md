---
title: Použití Pythonu k dotazování databáze
description: V tomto tématu se dozvíte, jak pomocí Pythonu vytvořit program, který se připojí k databázi v Azure SQL Database a provede dotaz pomocí příkazů jazyka Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: f21e11e33d3ddf1489dba3419766a8adaa878d5f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491958"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Rychlý Start: použití Pythonu k dotazování databáze

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

V tomto rychlém startu pomocí Pythonu se můžete připojit k Azure SQL Database, spravované instanci SQL Azure nebo synapse SQL Database a použít příkazy T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

Co budete potřebovat k dokončení tohoto rychlého startu:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Databáze, ve které budete spouštět dotaz.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 a související software
    

    |**Akce**|**macOS**|**Ubuntu**|**Windows**|
    |----------|-----------|------------|---------|
    |Instalace ovladače ODBC, SQLCMD a ovladače Python pro SQL Server|Při [vytváření aplikací v Pythonu pomocí SQL Server v MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)použijte kroky **1,2**, **1,3** a **2,1** . Tím se také nainstaluje instalace homebrew a Python.       |[Konfigurace prostředí pro vývoj v Pythonu pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux)|[Nakonfigurujte prostředí pro vývoj v Pythonu pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).|
    |Instalace Pythonu a dalších požadovaných balíčků|    |Použijte `sudo apt-get install python python-pip gcc g++ build-essential`.|    |
    |Další informace|[Ovladač Microsoft ODBC na macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)  |[Ovladač Microsoft ODBC na platformě Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|[Ovladač Microsoft ODBC na platformě Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server)|



Další prozkoumání Pythonu a databáze v Azure SQL Database najdete v tématu [knihovny Azure SQL Database pro Python](/python/api/overview/azure/sql), [úložiště pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)a [ukázku pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Vytvoření kódu pro dotazování databáze 

1. V textovém editoru vytvořte nový soubor s názvem *sqltest.py*.  
   
1. Přidejte následující kód. Získejte informace o připojení z oddílu požadavků a nahraďte vlastní hodnoty pro \<server> , \<database> , \<username> a \<password> .
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte následující příkaz:

   ```cmd
   python sqltest.py
   ```

1. Ověřte, zda jsou vráceny databáze a jejich kolace, a poté okno příkazového řádku zavřete.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze v Azure SQL Database](design-first-database-tutorial.md)
- [Ovladače Microsoft Pythonu pro SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Středisko pro vývojáře v Pythonu](https://azure.microsoft.com/develop/python/?v=17.23h)
