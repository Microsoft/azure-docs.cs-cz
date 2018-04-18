---
title: "Vytvoření SQL Data Warehouse pomocí prostředí PowerShell | Dokumentace Microsoftu"
description: "Vytvoření SQL Data Warehouse pomocí prostředí PowerShell"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: a763f1c600c1a3f37cb565a8eb7db3c3f27dcf75
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2017
---
# <a name="create-sql-data-warehouse-using-powershell"></a>Vytvoření SQL Data Warehouse pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

V tomto článku zjistíte, jak můžete k vytvoření SQL Data Warehouse použít PowerShell.

## <a name="prerequisites"></a>Požadavky
Na začátek budete potřebovat:

* **Účet Azure**: Pokud si chcete vytvořit účet, přejděte na stránku [Bezplatná zkušební verze Azure][Azure Free Trial] nebo [Kredity Azure pro předplatitele MSDN][MSDN Azure Credits].
* **Azure SQL Server:** Přečtěte si článek [Vytvoření Azure SQL Database pomocí webu Azure Portal][Create an Azure SQL database in the Azure Portal] nebo [Vytvoření Azure SQL Database pomocí prostředí PowerShell][Create an Azure SQL database with PowerShell], kde najdete další podrobnosti.
* **Skupinu prostředků:** Buď použijte stejnou skupinu prostředků jako pro Azure SQL Server, nebo zjistěte, [jak vytvořit skupinu prostředků](../azure-resource-manager/resource-group-portal.md).
* **Prostředí PowerShell verze 1.0.3 nebo novější:** To, jakou máte verzi, můžete zjistit spuštěním rutiny **Get-Module -ListAvailable -Name Azure**.  Nejnovější verzi si můžete nainstalovat pomocí [instalačního programu Webové platformy Microsoft][Microsoft Web Platform Installer].  Další informace o instalaci nejnovější verze najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell][How to install and configure Azure PowerShell].

> [!NOTE]
> Vytvoření služby SQL Data Warehouse může znamenat, že se vám začne fakturovat nová služba.  Další podrobnosti o cenách najdete v tématu [SQL Data Warehouse – ceny][SQL Data Warehouse pricing].
>
>

## <a name="create-a-sql-data-warehouse"></a>Vytvoření SQL Data Warehouse
1. Otevřete Windows PowerShell.
2. Spuštěním této rutiny se přihlaste do Azure Resource Manageru.

    ```Powershell
    Login-AzureRmAccount
    ```
3. Vyberte předplatné, které chcete použít pro aktuální relaci.

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Vytvořte databázi. Tento příklad vytvoří databázi s názvem mynewsqldw s úrovní cíle služby DW400 na serveru s názvem sqldwserver1, který je ve skupině prostředků s názvem mywesteuroperesgp1.

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Požadované parametry jsou:

* **RequestedServiceObjectiveName**: Počet jednotek [DWU][DWU], o které žádáte.  Podporované hodnoty: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 a DW6000.
* **DatabaseName**: Název služby SQL Data Warehouse, kterou vytváříte.
* **ServerName**: Název serveru, který používáte pro vytvoření (musí to být server V12).
* **ResourceGroupName**: Skupina prostředků, kterou používáte.  K vyhledání dostupných skupin prostředků v rámci vašeho předplatného použijte rutinu Get-AzureResource.
* **Edition:** Aby bylo možné vytvořit SQL Data Warehouse, je nutné nastavit edici DataWarehouse.

Volitelné parametry jsou:

* **CollationName:** Pokud není uvedeno, je výchozí kolace SQL_Latin1_General_CP1_CI_AS.  Kolaci nejde pro databázi změnit.
* **MaxSizeBytes:** Výchozí maximální velikost databáze je 10 GB.

Další podrobnosti o možných parametrech najdete v tématech [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] a [Vytvoření databáze (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Další kroky
Až se vám zřídí SQL Data Warehouse, můžete zkusit [načíst ukázková data][loading sample data] nebo se můžete podívat, jak na [vývoj][develop], [načítání][load] nebo [migraci][migrate].

Pokud vás zajímají další informace o tom, jak SQL Data Warehouse spravovat prostřednictvím kódu programu, podívejte se na náš článek věnovaný tomu, jak používat [rutiny prostředí PowerShell a rozhraní REST API][PowerShell cmdlets and REST APIs].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
