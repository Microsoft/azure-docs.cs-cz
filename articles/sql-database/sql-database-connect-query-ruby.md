---
title: Použití ruby k dotazování
description: Toto téma vám ukáže, jak pomocí Ruby vytvořit program, který se připojí ke službě Azure SQL Database a bude ji dotazovat s použitím příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 4bef55b049ee542efdb9d72d13fa196c989c75ec
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826926"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Rychlý start: Použití Ruby k dotazování databáze Azure SQL

Tento rychlý start ukazuje, jak používat [Ruby](https://www.ruby-lang.org) k připojení k databázi Azure SQL a dotazovat data pomocí příkazů Transact-SQL.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- Databázi Azure SQL. Pomocí jednoho z těchto rychlých startů můžete vytvořit a pak nakonfigurovat databázi v Azure SQL Database:

  || Izolovaná databáze | Spravovaná instance |
  |:--- |:--- |:---|
  | Vytvořit| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [Cli](scripts/sql-database-create-and-configure-database-cli.md) | [Cli](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall IP na úrovni serveru](sql-database-server-level-firewall-rule.md)| [Připojení z virtuálního virtuálního zařízení](sql-database-managed-instance-configure-vm.md)|
  |||[Připojení z místa na místě](sql-database-managed-instance-configure-p2s.md)
  |Načtení dat|Adventure Works načtený podle rychlého startu|[Obnovit wide world dovozci](sql-database-managed-instance-get-started-restore.md)
  |||Obnovení nebo import adventure works ze souboru [BACPAC](sql-database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou zapsány k použití databáze Adventure Works. Se spravovanou instancí je nutné buď importovat databázi adventure works do databáze instancí, nebo upravit skripty v tomto článku, aby bylo nutné používat databázi Wide World Importers.
  
- Ruby a související software pro váš operační systém:
  
  - **MacOS:** Nainstalujte Homebrew, rbenv a ruby-build, Ruby, FreeTDS a TinyTDS. Viz kroky 1.2, 1.3, 1.4, 1.5 a 2.1 v [tématu Vytvoření aplikací Ruby pomocí SQL Serveru v systému macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Nainstalujte předpoklady pro Ruby, rbenv a ruby-build, Ruby, FreeTDS a TinyTDS. Podívejte se na kroky 1.2, 1.3, 1.4, 1.5 a 2.1 v [tématu Vytvoření aplikací Ruby pomocí SQL Serveru na Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Nainstalujte Ruby, Ruby Devkit a TinyTDS. Viz [Konfigurace vývojového prostředí pro vývoj ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Získání informací o připojení k serveru SQL

Získejte informace o připojení, které potřebujete k připojení k databázi Azure SQL. Pro nadcházející postupy budete potřebovat úplný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Přejděte na stránku **databází SQL** nebo **instancí spravovaných SQL.**

3. Na stránce **Přehled** zkontrolujte úplný název serveru vedle **názvu serveru** pro jednu databázi nebo plně kvalifikovaný název serveru vedle **hostitele** pro spravovanou instanci. Chcete-li zkopírovat název serveru nebo název hostitele, najeďte na něj a vyberte ikonu **Kopírovat.** 

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. V editoru textu nebo kódu vytvořte nový soubor s názvem *sqltest.rb*.
   
1. Přidejte následující kód. Nahraďte hodnoty z databáze `<server>` `<database>`Azure `<username>`SQL `<password>`pro , , a .
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která si můžete vybrat jako zdroj při vytváření databáze. Pokud databáze obsahuje jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Spuštění kódu

1. Na příkazovém řádku spusťte následující příkaz:

   ```bash
   ruby sqltest.rb
   ```
   
1. Ověřte, zda je vráceno 20 nejvyšších řádků kategorie a produktu z databáze. 

## <a name="next-steps"></a>Další kroky
- [Navrhněte si první databázi Azure SQL](sql-database-design-first-database.md).
- [GitHub úložiště pro TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Nahlašte problémy nebo se ptejte na TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ovladač Ruby pro SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
