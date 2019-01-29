---
title: Použití Pythonu k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí Pythonu vytvořit program, který se připojí ke službě Azure SQL database a ji dotazovat s použitím příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/28/2019
ms.openlocfilehash: b611eb02203c872e3497b5b7c12acddd9eab14c0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188384"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Rychlý start: Použití Pythonu k dotazování databáze SQL Azure

 Tento rychlý start ukazuje použití [Pythonu](https://python.org) pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat. Další podrobnosti o sadě SDK, projděte si naše [odkaz](https://docs.microsoft.com/python/api/overview/azure/sql) dokumentaci, [úložiště GitHub pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)a [pyodbc ukázka](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Python a související software pro váš operační systém:
  
  - **MacOS**: Nainstalujte Homebrew a Python, nainstalujte ovladač ODBC a Nástroj SQLCMD a potom nainstalujte ovladač Python pro SQL Server. Najdete v krocích 1.2, 1.3 a 2.1 v [aplikace v Pythonu vytvořit pomocí SQL serveru v systému macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Další informace najdete v tématu [nainstalujte ovladač Microsoft ODBC Driver v systému Linux a macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).
    
  - **Ubuntu**: Nainstalujte Python a další požadované balíčky s `sudo apt-get install python python-pip gcc g++ build-essential`. Stáhněte si a nainstalujte ovladač ODBC, Nástroj SQLCMD a ovladač Python pro SQL Server. Pokyny najdete v tématu [nakonfigurujte vývojové prostředí pro vývoj v jazyce Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).
    
  - **Windows:** Nainstalujte Python, ovladač ODBC a Nástroj SQLCMD a ovladač Python pro SQL Server. Pokyny najdete v tématu [nakonfigurujte vývojové prostředí pro vývoj v jazyce Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL 

1. V textovém editoru, vytvořte nový soubor s názvem *sqltest.py*.  
   
1. Přidejte následující kód. Nahraďte vlastními hodnotami pro \<serveru >, \<databáze >, \<uživatelské jméno >, a \<heslo >.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu se používá ukázková data AdventureWorksLT, které můžete použít jako zdroj při vytváření databáze. Pokud vaše databáze má jiná data, pomocí tabulek z vlastní databázi v dotazu SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
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

1. Ověřte, že se vrátí prvních 20 řádků kategorie nebo produktů a potom zavřete příkazové okno.

## <a name="next-steps"></a>Další postup

- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)
- [Ovladače Microsoft Python pro SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Středisko pro vývojáře v Pythonu](https://azure.microsoft.com/develop/python/?v=17.23h)

