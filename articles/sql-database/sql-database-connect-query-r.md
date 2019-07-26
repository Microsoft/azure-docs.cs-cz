---
title: Použití R s Machine Learning Services k dotazování Azure SQL Database
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: V tomto článku se dozvíte, jak použít skript R s Azure SQL Database Machine Learning Services pro připojení k databázi SQL Azure a dotazování s použitím příkazů jazyka Transact-SQL.
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
ms.openlocfilehash: ff38346a9b3bd14db51383c116240b030d3ee42a
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68514862"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Rychlý start: Použití R s Machine Learning Services k dotazování služby Azure SQL Database (Preview)

V tomto rychlém startu se dozvíte, jak použít [R](https://www.r-project.org/) se Machine Learning Services pro připojení k databázi SQL Azure a použití příkazů jazyka Transact-SQL k dotazování dat. Machine Learning Services je funkce Azure SQL Database, která se používá ke spouštění skriptů R v databázi. Další informace najdete v tématu [Azure SQL Database Machine Learning Services s R (Preview)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

- Databázi Azure SQL. K vytvoření a konfiguraci databáze v Azure SQL Database můžete použít jeden z těchto rychlých startů:

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
  | Create| [Azure Portal](sql-database-single-database-get-started.md) |
  || [Rozhraní příkazového řádku](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurace | [Pravidlo brány firewall protokolu IP na úrovni serveru](sql-database-server-level-firewall-rule.md) |
  | Načtení dat | Načtený Adventure Works pro každý rychlý Start |
  |||

  > [!NOTE]
  > V rámci verze Preview Azure SQL Database Machine Learning Services s R není možnost nasazení Managed instance podporována.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services (s povoleným R). V rámci verze Public Preview vám Microsoft zaregistruje a povolí Machine Learning pro vaši stávající nebo novou databázi. Postupujte podle kroků v [části registrace ve verzi Preview](sql-database-machine-learning-services-overview.md#signup).

- Nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spouštět skripty R pomocí jiných nástrojů pro správu databáze nebo dotazů, ale v tomto rychlém startu použijete SSMS.

## <a name="get-sql-server-connection-information"></a>Získat informace o připojení k SQL serveru

Získejte informace o připojení, které potřebujete pro připojení ke službě Azure SQL Database. Pro nadcházející postupy budete potřebovat plně kvalifikovaný název serveru nebo název hostitele, název databáze a přihlašovací údaje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Přejděte na stránku **databáze SQL** nebo **spravované instance SQL** .

3. Na stránce **Přehled** zkontrolujte plně kvalifikovaný název serveru vedle **názvu serveru** pro izolovanou databázi nebo plně kvalifikovaný název serveru vedle možnost **hostitel** pro spravovanou instanci. Pokud chcete zkopírovat název serveru nebo název hostitele, najeďte na něj ukazatelem myši a vyberte ikonu **kopírování** .

## <a name="create-code-to-query-your-sql-database"></a>Vytvoření kódu pro dotazování databáze SQL

1. Otevřete aplikaci **SQL Server Management Studio** a připojte se ke své databázi SQL.

   Pokud potřebujete pomáhat s připojením, přečtěte si [rychlý Start: Pomocí SQL Server Management Studio se můžete připojit k databázi](sql-database-connect-query-ssms.md)SQL Azure a dotazovat se na ně.

1. Předejte kompletní skript R do uložené procedury [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

   Skript se předává přes `@script` argument. Vše uvnitř `@script` argumentu musí být platný kód R.
   
   >[!IMPORTANT]
   >Kód v tomto příkladu používá ukázková data AdventureWorksLT, která můžete zvolit jako zdroj při vytváření databáze. Pokud má vaše databáze jiná data, použijte tabulky z vlastní databáze v dotazu SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Pokud se zobrazí nějaké chyby, může to být proto, že služba Machine Learning Services (s jazykem R) verze Public Preview není pro vaši databázi SQL povolená. Viz [předpoklady](#prerequisites) výše.

## <a name="run-the-code"></a>Spuštění kódu

1. Spusťte uloženou proceduru [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

1. Ověřte, zda je v okně **zprávy** vráceno prvních 20 řádků kategorie/produktu.

## <a name="next-steps"></a>Další postup

- [Návrh první databáze SQL Azure](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (s R)](sql-database-machine-learning-services-overview.md)
- [Vytvoření a spuštění jednoduchých skriptů R v Azure SQL Database Machine Learning Services (Preview)](sql-database-quickstart-r-create-script.md)
- [Zápis pokročilých funkcí R v Azure SQL Database pomocí Machine Learning Services (Preview)](sql-database-machine-learning-services-functions.md)
