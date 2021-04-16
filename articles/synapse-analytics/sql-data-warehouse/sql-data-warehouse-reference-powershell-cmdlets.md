---
title: Rozhraní API REST & pro vyhrazený fond SQL (dřív SQL DW)
description: Hlavní rutiny PowerShellu pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics, včetně toho, jak pozastavit a obnovit databázi.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 1f00f470fb0aa8ac98b431c6fc9428f501b553ed
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566439"
---
# <a name="powershell--rest-apis-for-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Prostředí PowerShell & rozhraní REST API pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics 

Mnoho vyhrazených úloh správy fondů SQL se dá spravovat pomocí rutin Azure PowerShell nebo rozhraní REST API.  Níže uvádíme několik příkladů použití příkazů PowerShellu k automatizaci běžných úloh ve vyhrazeném fondu SQL (dřív SQL DW).  Některé dobré příklady REST najdete v článku [Správa škálovatelnosti pomocí REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Začínáme s rutinami Azure PowerShell

1. Otevřete Windows PowerShell.
2. Spuštěním těchto příkazů na příkazovém řádku PowerShellu se přihlaste k Azure Resource Manager a vyberte své předplatné.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Příklad pozastavení datového skladu

Pozastavení databáze s názvem "Database02", která je hostována na serveru s názvem "Server01".  Server je ve skupině prostředků Azure s názvem "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Varianta, v tomto příkladu, přeruší načtený objekt na [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  V důsledku toho je databáze pozastavena. Poslední příkaz zobrazí výsledky.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Příklad spuštění datového skladu

Operace obnovení databáze s názvem "Database02", která je hostována na serveru s názvem "Server01". Server je součástí skupiny prostředků s názvem "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

V tomto příkladu se v tomto příkladu načte databáze s názvem "Database02" ze serveru s názvem "Server01", který je součástí skupiny prostředků s názvem "ResourceGroup1". Napředá načtený objekt pro [pokračování – AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Všimněte si, že pokud je váš server foo.database.windows.net, použijte v rutinách PowerShellu "foo" jako "servername".

## <a name="other-supported-powershell-cmdlets"></a>Další podporované rutiny PowerShellu

Tyto rutiny PowerShellu se podporují s datovým skladem Azure synapse Analytics.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Obnovit – AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Pokračovat – AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Pozastavit – AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Další kroky

Další příklady PowerShellu najdete v tématech:

* [Vytvoření datového skladu pomocí prostředí PowerShell](create-data-warehouse-powershell.md)
* [Obnovení databáze](sql-data-warehouse-restore-points.md)

Další úkoly, které je možné automatizovat pomocí prostředí PowerShell, najdete v tématu [rutiny Azure SQL Database](/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Pro datový sklad Azure synapse Analytics nejsou podporovány všechny rutiny Azure SQL Database. Seznam úloh, které je možné automatizovat pomocí REST, najdete v tématu [operace pro Azure SQL Database](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
