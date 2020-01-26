---
title: Použití Pythonu k dotazování databáze
description: V tomto tématu se dozvíte, jak pomocí Pythonu vytvořit program, který se připojí ke službě Azure SQL Database a provede dotaz pomocí příkazů jazyka Transact-SQL.
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
ms.openlocfilehash: 9929c483c2e27983254033e2e26b3b753699260b
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758304"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Rychlý start: Použití Pythonu k dotazování databáze Azure SQL

V tomto rychlém startu pomocí Pythonu se připojíte k databázi SQL Azure a použijete k dotazování dat příkazy T-SQL.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [Databáze SQL Azure](sql-database-single-database-get-started.md)
  
- [Python](https://python.org/downloads) 3 a související software

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  K instalaci homebrew a Pythonu, ovladače ODBC a nástroje SQLCMD a ovladače Pythonu pro SQL Server použijte kroky **1,2**, **1,3**a **2,1** v části [vytváření aplikací v Pythonu pomocí SQL Server na MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Další informace najdete v tématu [ovladač Microsoft ODBC na MacOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Pokud chcete nainstalovat Python a další požadované balíčky, použijte `sudo apt-get install python python-pip gcc g++ build-essential`.

  Informace o instalaci ovladače ODBC, SQLCMD a ovladače Pythonu pro SQL Server najdete v tématu [Konfigurace prostředí pro vývoj Pythonu v pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Další informace najdete v tématu [ovladač Microsoft ODBC na platformě Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Informace o instalaci Pythonu, ovladače ODBC a nástroje SQLCMD a ovladače Pythonu pro SQL Server najdete v tématu [Konfigurace prostředí pro vývoj Pythonu v pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Další informace najdete v tématu [ovladač Microsoft ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Skripty v tomto článku jsou určeny k používání databáze **Adventure Works** .

> [!NOTE]
> Volitelně můžete zvolit použití spravované instance Azure SQL.
>
> K vytvoření a konfiguraci použijte [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)nebo [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)a pak nastavte připojení [na pracovišti](sql-database-managed-instance-configure-p2s.md) nebo [virtuálním počítači](sql-database-managed-instance-configure-vm.md) .
>
> Pokud chcete načíst data, přečtěte si téma [Restore with BacPac](sql-database-import.md) se souborem [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) nebo si přečtěte část [obnovení databáze World Importers](sql-database-managed-instance-get-started-restore.md).

Další prozkoumání Pythonu a databáze SQL Azure najdete v tématech [knihovny Azure SQL Database pro Python](/python/api/overview/azure/sql), [úložiště pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)a [Ukázka pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [Portálu Azure](https://portal.azure.com/).

2. Přejít na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL 

1. V textovém editoru vytvořte nový soubor s názvem *sqltest.py*.  
   
1. Přidejte následující kód. Nahraďte vlastní hodnoty pro \<Server >, \<Database >, \<username > a \<Password >.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která můžete zvolit jako zdroj při vytváření databáze. Pokud má vaše databáze jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 
   
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

1. Ověřte, že jsou vraceny prvních 20 řádků kategorie/produktu, a pak zavřete příkazové okno.

## <a name="next-steps"></a>Další kroky

- [Návrh první databáze Azure SQL](sql-database-design-first-database.md)
- [Ovladače Microsoft Pythonu pro SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Středisko pro vývojáře v Pythonu](https://azure.microsoft.com/develop/python/?v=17.23h)

