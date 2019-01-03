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
ms.date: 12/20/2018
ms.openlocfilehash: 66819cbd65f6f044d0dac68326eb5890476964b6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993905"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Rychlý start: Použití Ruby k dotazování na službu Azure SQL Database

Tento rychlý start ukazuje, jak používat [Ruby](https://www.ruby-lang.org) pro připojení Azure SQL database a dotazování dat pomocí příkazů jazyka Transact-SQL.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat následující:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- [Pravidlo brány firewall na úrovni serveru](sql-database-get-started-portal-firewall.md) pro veřejnou IP adresu počítače, který používáte pro tento rychlý start.
  
- Ruby a související software pro váš operační systém:
  
  - **macOS**: Nainstalujte Homebrew, rbenv a ruby-build, Ruby, FreeTDS a TinyTDS. Najdete v krocích 1.2, 1.3, 1.4, 1.5 a 2.1 v [Ruby vytvořit aplikace s využitím SQL serveru v systému macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Instalace požadovaných součástí pro Ruby, rbenv a ruby-build, Ruby, FreeTDS a TinyTDS. Najdete v krocích 1.2, 1.3, 1.4, 1.5 a 2.1 v [Ruby vytvořit aplikace s využitím SQL serveru na Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows:** Nainstalujte Ruby, Ruby Devkit a TinyTDS. Zobrazit [konfigurace vývojového prostředí o vývoji v Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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
