---
title: Api rest prostředí PowerShell &
description: Najděte nejlepší rutiny Prostředí PowerShell pro fond SQL Azure Synapse Analytics, včetně toho, jak pozastavit a obnovit databázi.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 644ad931879c1953560abf13ac0eda58f3d814d8
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633067"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell & REST API pro fond SQL Azure Synapse Analytics

Mnoho úloh správy fondu SQL Azure Synapse Analytics lze spravovat pomocí rutin Azure PowerShell nebo REST API.  Níže jsou uvedeny některé příklady, jak pomocí příkazů prostředí PowerShell automatizovat běžné úkoly ve fondu SQL.  Některé dobré příklady REST najdete v článku [Správa škálovatelnosti pomocí REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Začínáme s rutinami Azure PowerShellu

1. Otevřete Windows PowerShell.
2. Na příkazovém řádku Prostředí PowerShell spusťte tyto příkazy a přihlaste se ke Správci prostředků Azure a vyberte předplatné.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Příklad pozastavení datového skladu

Pozastavit databázi s názvem "Database02" hostovací na serveru s názvem Server01.  Server se nachází ve skupině prostředků Azure s názvem Skupina prostředků1.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Varianta, v tomto příkladu kanály načtený objekt [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  V důsledku toho je databáze pozastavena. Konečný příkaz zobrazí výsledky.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Příklad spuštění datového skladu

Obnovit provoz databáze s názvem "Database02" hostované na serveru s názvem Server01. Server je obsažen ve skupině prostředků s názvem Skupina prostředků1.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Varianta, tento příklad načte databázi s názvem "Database02" ze serveru s názvem "Server01", který je obsažen ve skupině prostředků s názvem ResourceGroup1. To kanály načtený objekt [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Všimněte si, že pokud je váš server foo.database.windows.net, použijte "foo" jako -ServerName v rutinách prostředí PowerShell.

## <a name="other-supported-powershell-cmdlets"></a>Další podporované rutiny prostředí PowerShell

Tyto rutiny prostředí PowerShell jsou podporované datovým skladem Azure Synapse Analytics.

* [Databáze Get-AzSql](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedZálohování databáze](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabázeObnovit bod](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Nová databáze AzSql](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Odebrat databázi AzSql](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase]/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/strouhanka/toc.json)
* [Databáze Životopis-AzSql](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabáze](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Databáze pozastavit azsql](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Další kroky

Další příklady Prostředí PowerShell najdete v tématu:

* [Vytvoření datového skladu pomocí PowerShellu](create-data-warehouse-powershell.md)
* [Obnovení databáze](sql-data-warehouse-restore-points.md)

Další úkoly, které lze automatizovat pomocí prostředí PowerShell, najdete v tématu [Rutiny Azure SQL Database]/powershell/module/az.sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/strouhanka/toc.json). Ne všechny rutiny Azure SQL Database jsou podporované pro datový sklad Azure Synapse Analytics. Seznam úloh, které lze automatizovat pomocí rest, najdete v [tématu Operace pro Azure SQL Database](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
