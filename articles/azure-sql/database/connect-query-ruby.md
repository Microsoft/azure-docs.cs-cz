---
title: Použít Ruby k dotazování
description: V tomto tématu se dozvíte, jak pomocí Ruby vytvořit program, který se připojí k databázi v Azure SQL Database a provede dotaz pomocí příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 1a0f65ff6d0b98d3764cb6a7b1d4b07ccb8ae92a
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054405"
---
# <a name="quickstart-use-ruby-to-query-a-microsoft-azure-sql-database"></a>Rychlý Start: použití Ruby k dotazování na Microsoft Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento rychlý Start ukazuje, jak se pomocí [Ruby](https://www.ruby-lang.org) připojit k databázi SQL Azure a dotazovat data pomocí příkazů jazyka Transact-SQL.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL můžete použít jeden z těchto rychlých startů:

  || SQL Database | Spravovaná instance SQL | SQL Server na virtuálním počítači Azure |
  |:--- |:--- |:---|:---|
  | Vytvořit| [Azure Portal](single-database-create-quickstart.md) | [Azure Portal](../managed-instance/instance-create-quickstart.md) | [Azure Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Rozhraní příkazového řádku](scripts/create-and-configure-database-cli.md) | [Rozhraní příkazového řádku](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](firewall-create-server-level-portal-quickstart.md)| [Připojení z virtuálního počítače](../managed-instance/connect-vm-instance-configure.md)|
  |||[Připojení z webu](../managed-instance/point-to-site-p2s-configure.md) | [Připojení k SQL Serveru](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Načtení dat|Načtený Adventure Works pro každý rychlý Start|[Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) | [Obnovení celosvětových dovozců](../managed-instance/restore-sample-database-quickstart.md) |
  |||Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Obnovení nebo import Adventure Works ze souboru [BacPac](database-import.md) z [GitHubu](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripty v tomto článku jsou určeny k používání databáze Adventure Works. Se spravovanou instancí musíte buď importovat databázi Adventure Works do databáze instance, nebo upravit skripty v tomto článku, aby používaly databázi World Importers.
  
- Ruby a související software pro váš operační systém:
  
  - **MacOS**: Nainstalujte homebrew, rbenv a Ruby-Build, Ruby, FreeTDS a TinyTDS. V tématu [vytváření aplikací Ruby pomocí SQL Server v MacOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)najdete postup 1,2, 1,3, 1,4, 1,5 a 2,1.
  
  - **Ubuntu**: požadavky na instalaci pro Ruby, rbenv a Ruby-Build, Ruby, FreeTDS a TinyTDS. V tématu [vytváření aplikací Ruby pomocí SQL Server v Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)najdete postup 1,2, 1,3, 1,4, 1,5 a 2,1.
  
  - **Windows**: Nainstalujte Ruby, Ruby DevKit a TinyTDS. Viz [Konfigurace vývojového prostředí pro vývoj v Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** Azure SQL Database nebo plně kvalifikovaného názvu serveru (nebo IP adresy) vedle **hostitele** spravované instance Azure SQL nebo SQL Server ve virtuálním počítači Azure. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

> [!NOTE]
> Informace o připojení pro SQL Server na virtuálním počítači Azure najdete v tématu [připojení k SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. V textovém editoru nebo editoru kódu vytvořte nový soubor s názvem *sqltest. RB*.

1. Přidejte následující kód. Nahraďte hodnoty z vaší databáze SQL Azure pro `<server>` , `<database>` , `<username>` a `<password>` .

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
- [Navrhněte první Azure SQL Database](design-first-database-tutorial.md).
- [Úložiště GitHub pro TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Oznamte problémy nebo položte otázky týkající se TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ovladač Ruby pro SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
