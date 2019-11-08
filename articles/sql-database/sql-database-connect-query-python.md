---
title: 'Rychlý Start: použití Pythonu k dotazování'
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
ms.openlocfilehash: 42d5b500a48e427aad2372710597e0266b2e80aa
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827000"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Rychlý start: Použití Pythonu k dotazování databáze Azure SQL

 Tento článek ukazuje, jak použít [Python](https://python.org) pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat. Další podrobnosti o sadě SDK najdete v naší [referenční](https://docs.microsoft.com/python/api/overview/azure/sql) dokumentaci, [úložišti GitHubu pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)a [ukázce pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

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
  
- Python a související software pro váš operační systém:
  
  - **MacOS**: Nainstalujte homebrew a Python, nainstalujte ovladač ODBC a Nástroj Sqlcmd a potom nainstalujte ovladač Python pro SQL Server. V tématu [vytváření aplikací v Pythonu pomocí SQL Server v MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/)najdete kroky 1,2, 1,3 a 2,1. Další informace najdete v tématu [instalace ovladače Microsoft ODBC na platformě Linux a MacOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Nainstalujte Python a další požadované balíčky s `sudo apt-get install python python-pip gcc g++ build-essential`. Stáhněte a nainstalujte ovladač ODBC, SQLCMD a ovladač Pythonu pro SQL Server. Pokyny najdete v tématu [Konfigurace vývojového prostředí pro vývoj Pythonu v pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows**: Nainstalujte Python, ovladač ODBC a Sqlcmd a ovladač Python pro SQL Server. Pokyny najdete v tématu [Konfigurace vývojového prostředí pro vývoj Pythonu v pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

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

