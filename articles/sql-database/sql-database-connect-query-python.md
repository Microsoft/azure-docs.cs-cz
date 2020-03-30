---
title: Použití Pythonu k dotazování na databázi
description: Toto téma ukazuje, jak pomocí Pythonu vytvořit program, který se připojí k databázi Azure SQL a dotazovat se na něj pomocí příkazů Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768577"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Rychlý start: Použití Pythonu k dotazování databáze Azure SQL

V tomto rychlém startu použijete Python k připojení k databázi Azure SQL a pomocí příkazů T-SQL k dotazování dat.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Databáze Azure SQL](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 a související software

  # <a name="macos"></a>[Macos](#tab/macos)

  Chcete-li nainstalovat Homebrew a Python, ovladač ODBC a SQLCMD a ovladač Pythonu pro SQL Server, použijte kroky **1.2**, **1.3**a **2.1** při [vytváření aplikací Pythonu pomocí SQL Serveru v systému macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Další informace naleznete v [tématu Microsoft ODBC Driver on macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Chcete-li nainstalovat Python a `sudo apt-get install python python-pip gcc g++ build-essential`další požadované balíčky, použijte .

  Chcete-li nainstalovat ovladač ODBC, SQLCMD a ovladač Pythonu pro SQL Server, přečtěte si [informace o konfiguraci prostředí pro vývoj pyodbc Pythonu](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Další informace naleznete v [tématu Microsoft ODBC Driver on Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Chcete-li nainstalovat Python, ovladač ODBC a SQLCMD a ovladač Pythonu pro SQL Server, přečtěte si [informace o konfiguraci prostředí pro vývoj pyodbc Pythonu](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Další informace naleznete v tématu [Microsoft ODBC Driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Skripty v tomto článku jsou zapsány k použití databáze **Adventure Works.**

> [!NOTE]
> Volitelně můžete zvolit použití spravované instance Azure SQL.
>
> Chcete-li vytvořit a nakonfigurovat, použijte [portál Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)nebo [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)a nastavte připojení [na místě](sql-database-managed-instance-configure-p2s.md) nebo [v počítači.](sql-database-managed-instance-configure-vm.md)
>
> Chcete-li načíst data, viz [obnovení pomocí BACPAC](sql-database-import.md) se souborem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) nebo naleznete [v tématu obnovení databáze Wide World Importers](sql-database-managed-instance-get-started-restore.md).

Další informace o Pythonu a databázi Azure SQL najdete v [tématu Knihovny databází Azure SQL pro Python](/python/api/overview/azure/sql), úložiště [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)a [ukázku pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.**

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL 

1. V textovém editoru vytvořte nový soubor s názvem *sqltest.py*.  
   
1. Přidejte následující kód. Nahraďte vlastní \<hodnoty \<> serveru, databázových \< \<>,> uživatelského jména a> hesla.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která si můžete vybrat jako zdroj při vytváření databáze. Pokud databáze obsahuje jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 
   
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

1. Ověřte, zda je vráceno prvních 20 řádků kategorie/produktu, a zavřete příkazové okno.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)
- [Ovladače Microsoft Pythonu pro SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Vývojářské centrum Pythonu](https://azure.microsoft.com/develop/python/?v=17.23h)

