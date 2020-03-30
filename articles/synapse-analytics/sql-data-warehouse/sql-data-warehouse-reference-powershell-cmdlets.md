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
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351388"
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
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Další podporované rutiny prostředí PowerShell
Tyto rutiny prostředí PowerShell jsou podporované datovým skladem Azure Synapse Analytics.

* [Databáze Get-AzSql](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedZálohování databáze](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabázeObnovit bod](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [Nová databáze AzSql](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Odebrat databázi AzSql](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Obnovit databázi AzSql](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Databáze Životopis-AzSql](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabáze](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Databáze pozastavit azsql](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Další kroky
Další příklady Prostředí PowerShell najdete v tématu:

* [Vytvoření datového skladu pomocí PowerShellu](create-data-warehouse-powershell.md)
* [Obnovení databáze](sql-data-warehouse-restore-points.md)

Další úkoly, které lze automatizovat pomocí PowerShellu, najdete v článku [rutiny Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql). Ne všechny rutiny Azure SQL Database jsou podporované pro datový sklad Azure Synapse Analytics.  Seznam úloh, které lze automatizovat pomocí rest, najdete v [tématu Operace pro Azure SQL Database](/rest/api/sql/).
