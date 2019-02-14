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
ms.date: 02/12/2019
ms.openlocfilehash: d0dcfa3d5e387b03fe76eff34e32860ae5b17e76
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235362"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Rychlý start: Použití Pythonu k dotazování databáze SQL Azure

 Tento rychlý start ukazuje použití [Pythonu](https://python.org) pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat. Další podrobnosti o sadě SDK, projděte si naše [odkaz](https://docs.microsoft.com/python/api/overview/azure/sql) dokumentaci, [úložiště GitHub pyodbc](https://github.com/mkleehammer/pyodbc/wiki/)a [pyodbc ukázka](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Databázi SQL Azure. Jeden z těchto rychlých startech můžete vytvořit a potom nakonfigurovat databázi ve službě Azure SQL Database:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvořit| [Azure Portal](sql-database-single-database-get-started.md) | [Azure Portal](sql-database-managed-instance-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Konfigurace | [pravidlo brány firewall na úrovni serveru IP](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního počítače](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z na místě](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Společnosti Adventure Works načtených za rychlý start|[Obnovit Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo importovat společnosti Adventure Works z [BACPAC](sql-database-import.md) souboru z [githubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku se zapisují do použít databázi společnosti Adventure Works. S managed instance musíte importovat databázi společnosti Adventure Works do instance databáze nebo upravovat skripty v tomto článku pro používání databáze Wide World Importers.
  
- Python a související software pro váš operační systém:
  
  - **MacOS**: Nainstalujte Homebrew a Python, nainstalujte ovladač ODBC a Nástroj SQLCMD a potom nainstalujte ovladač Python pro SQL Server. Najdete v krocích 1.2, 1.3 a 2.1 v [aplikace v Pythonu vytvořit pomocí SQL serveru v systému macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Další informace najdete v tématu [nainstalujte ovladač Microsoft ODBC Driver v systému Linux a macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Nainstalujte Python a další požadované balíčky s `sudo apt-get install python python-pip gcc g++ build-essential`. Stáhněte si a nainstalujte ovladač ODBC, Nástroj SQLCMD a ovladač Python pro SQL Server. Pokyny najdete v tématu [nakonfigurujte vývojové prostředí pro vývoj v jazyce Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows:** Nainstalujte Python, ovladač ODBC a Nástroj SQLCMD a ovladač Python pro SQL Server. Pokyny najdete v tématu [nakonfigurujte vývojové prostředí pro vývoj v jazyce Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

Získejte informace o připojení potřebné pro připojení k databázi Azure SQL. Nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte **databází SQL** nebo **spravované instance SQL** stránky.

3. Na **přehled** stránce si prohlédněte plně kvalifikovaný název vedle **název serveru** pro izolované databáze nebo serveru plně kvalifikovaný název vedle **hostitele** pro spravované instance. Zkopírujte název serveru nebo název hostitele, je ukazatel myši a vyberte **kopírování** ikonu.

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

