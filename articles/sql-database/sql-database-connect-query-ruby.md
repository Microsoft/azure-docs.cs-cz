---
title: Použití Ruby k dotazování služby Azure SQL Database | Dokumentace Microsoftu
description: Toto téma vám ukáže, jak pomocí Ruby vytvořit program, který se připojí ke službě Azure SQL Database a bude ji dotazovat s použitím příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: bea32acf4028a4ab43fcc8cd967a31ea397babf3
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234216"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Rychlý start: Použití Ruby k dotazování na službu Azure SQL Database

Tento rychlý start ukazuje, jak používat [Ruby](https://www.ruby-lang.org) pro připojení Azure SQL database a dotazování dat pomocí příkazů jazyka Transact-SQL.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat následující:

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
  
- Ruby a související software pro váš operační systém:
  
  - **MacOS**: Nainstalujte Homebrew, rbenv a ruby-build, Ruby, FreeTDS a TinyTDS. Najdete v krocích 1.2, 1.3, 1.4, 1.5 a 2.1 v [Ruby vytvořit aplikace s využitím SQL serveru v systému macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Instalace požadovaných součástí pro Ruby, rbenv a ruby-build, Ruby, FreeTDS a TinyTDS. Najdete v krocích 1.2, 1.3, 1.4, 1.5 a 2.1 v [Ruby vytvořit aplikace s využitím SQL serveru na Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows:** Nainstalujte Ruby, Ruby Devkit a TinyTDS. Zobrazit [konfigurace vývojového prostředí o vývoji v Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

Získejte informace o připojení potřebné pro připojení k databázi Azure SQL. Nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte **databází SQL** nebo **spravované instance SQL** stránky.

3. Na **přehled** stránce si prohlédněte plně kvalifikovaný název vedle **název serveru** pro izolované databáze nebo serveru plně kvalifikovaný název vedle **hostitele** pro spravované instance. Zkopírujte název serveru nebo název hostitele, je ukazatel myši a vyberte **kopírování** ikonu. 

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. V editoru kódu nebo textu, vytvořte nový soubor s názvem *sqltest.rb*.
   
1. Přidejte následující kód. Nahraďte hodnotami z Azure SQL database pro `<server>`, `<database>`, `<username>`, a `<password>`.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu se používá ukázková data AdventureWorksLT, které můžete použít jako zdroj při vytváření databáze. Pokud vaše databáze má jiná data, pomocí tabulek z vlastní databázi v dotazu SELECT. 
   
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
   
1. Ověřte, že se vrátí prvních 20 řádků kategorie nebo produktů z databáze. 

## <a name="next-steps"></a>Další postup
- [Návrh první databáze Azure SQL database](sql-database-design-first-database.md).
- [Úložiště GitHub pro TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Hlášení problémů nebo kladení dotazů ohledně TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ovladače Ruby pro SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
