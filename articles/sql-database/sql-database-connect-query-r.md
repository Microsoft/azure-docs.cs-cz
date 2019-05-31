---
title: Použití R k dotazování služby Azure SQL Database
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Tento článek ukazuje, jak použít skript jazyka R pro připojení k databázi Azure SQL a ji dotazovat s použitím příkazů jazyka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 1d4b17cf1e0349bf877c676cb4e591fc20ad4113
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416361"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Rychlý start: Použití R k dotazování Azure SQL database (preview)

 Tento rychlý start ukazuje, jak používat [R](https://www.r-project.org/) službou Machine Learning Services k připojení k databázi Azure SQL a použití příkazů jazyka Transact-SQL k dotazování dat. Služby Machine Learning je funkce služby Azure SQL Database, používá se ke spuštění skriptů jazyka R v databázi. Další informace najdete v tématu [SQL databáze služeb Azure Machine Learning s jazykem R (preview)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Databázi Azure SQL. Jeden z těchto rychlých startech můžete vytvořit a potom nakonfigurovat databázi ve službě Azure SQL Database:

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Izolovaná databáze |
  |:--- |:--- |
  | Vytvořit| [Azure Portal](sql-database-single-database-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurace | [pravidlo brány firewall na úrovni serveru IP](sql-database-server-level-firewall-rule.md) |
  | Načtení dat | Společnosti Adventure Works načtených za rychlý start |
  |||

  > [!NOTE]
  > Ve verzi preview Azure SQL Database služby Machine Learning s jazykem R není podporována možnost nasazení spravované instance.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services (s jazykem R) povolena. Ve verzi public preview, společnost Microsoft bude připojit je a povolit strojového učení pro existující nebo nové databáze. Postupujte podle kroků v [zaregistrovat verzi preview](sql-database-machine-learning-services-overview.md#signup).

- Nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spustit skripty R používat správu jiných databází nebo nástrojů pro dotazování, ale v tomto rychlém startu budete používat SSMS.

## <a name="get-sql-server-connection-information"></a>Získejte informace o připojení SQL serveru

Získejte informace o připojení potřebné pro připojení k databázi Azure SQL. Nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte **databází SQL** nebo **spravované instance SQL** stránky.

3. Na **přehled** stránce si prohlédněte plně kvalifikovaný název vedle **název serveru** pro izolované databáze nebo serveru plně kvalifikovaný název vedle **hostitele** pro spravované instance. Zkopírujte název serveru nebo název hostitele, je ukazatel myši a vyberte **kopírování** ikonu.

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. Otevřete aplikaci **SQL Server Management Studio** a připojte se ke své databázi SQL.

   Pokud potřebujete pomoc s připojením, přečtěte si [rychlý start: Pomocí SQL Server Management Studio k připojení a dotazování Azure SQL database](sql-database-connect-query-ssms.md).

1. Předat kompletní skript R, abyste [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) uložené procedury.

   Skript se předává `@script` argument. Všechno, co je uvnitř `@script` argument musí být platný kód R.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu se používá ukázková data AdventureWorksLT, které můžete použít jako zdroj při vytváření databáze. Pokud vaše databáze má jiná data, pomocí tabulek z vlastní databázi v dotazu SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Pokud se zobrazí nějaké chyby, může to být proto, že služba Machine Learning Services (s jazykem R) verze Public Preview není pro vaši databázi SQL povolená. Zobrazit [požadavky](#prerequisites) výše.

## <a name="run-the-code"></a>Spuštění kódu

1. Spustit [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) uložené procedury.

1. Ověřte, zda jsou v vrátil prvních 20 řádků kategorie/produktu **zprávy** okna.

## <a name="next-steps"></a>Další postup

- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)
- [Azure SQL Database služby Machine Learning (s jazykem R)](sql-database-machine-learning-services-overview.md)
- [Vytvořit a spustit jednoduché skripty jazyka R v Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md)
- [Zápis pokročilé funkce jazyka R ve službě Azure SQL Database pomocí služby Machine Learning (preview)](sql-database-machine-learning-services-functions.md)
