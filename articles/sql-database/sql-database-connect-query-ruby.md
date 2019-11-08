---
title: Použít Ruby k dotazování
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826926"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Rychlý start: Použití Ruby k dotazování databáze Azure SQL

Tento rychlý Start ukazuje, jak se pomocí [Ruby](https://www.ruby-lang.org) připojit k databázi SQL Azure a dotazovat data pomocí příkazů jazyka Transact-SQL.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete následující požadavky:

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
  
- Ruby a související software pro váš operační systém:
  
  - **MacOS**: Nainstalujte homebrew, rbenv a Ruby-Build, Ruby, FreeTDS a TinyTDS. V tématu [vytváření aplikací Ruby pomocí SQL Server v MacOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)najdete postup 1,2, 1,3, 1,4, 1,5 a 2,1.
  
  - **Ubuntu**: požadavky na instalaci pro Ruby, rbenv a Ruby-Build, Ruby, FreeTDS a TinyTDS. V tématu [vytváření aplikací Ruby pomocí SQL Server v Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)najdete postup 1,2, 1,3, 1,4, 1,5 a 2,1.
  
  - **Windows**: Nainstalujte Ruby, Ruby DevKit a TinyTDS. Viz [Konfigurace vývojového prostředí pro vývoj v Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** . 

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. V textovém editoru nebo editoru kódu vytvořte nový soubor s názvem *sqltest. RB*.
   
1. Přidejte následující kód. Nahraďte hodnoty z vaší databáze SQL Azure pro `<server>`, `<database>`, `<username>`a `<password>`.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která můžete zvolit jako zdroj při vytváření databáze. Pokud má vaše databáze jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 
   
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
   
1. Ověřte, že jsou vraceny první 20 řádků kategorie/produktu z vaší databáze. 

## <a name="next-steps"></a>Další kroky
- [Navrhněte svou první databázi SQL Azure](sql-database-design-first-database.md).
- [Úložiště GitHub pro TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Oznamte problémy nebo položte otázky týkající se TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ovladač Ruby pro SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
