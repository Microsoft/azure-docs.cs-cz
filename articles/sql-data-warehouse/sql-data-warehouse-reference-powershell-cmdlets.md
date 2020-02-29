---
title: Rozhraní REST API & PowerShellu
description: Najděte si hlavní rutiny PowerShellu pro fond SQL synapse Analytics pro Azure, včetně toho, jak pozastavit a obnovit databázi.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0c8b1e9b7526bd45d037f053715613b53ec163f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198452"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>Rozhraní API REST & PowerShellu pro fond SQL Azure synapse Analytics

Mnohé úlohy správy fondu SQL Azure synapse Analytics se dají spravovat pomocí rutin Azure PowerShell nebo rozhraní REST API.  Níže jsou uvedeny některé příklady použití příkazů PowerShellu k automatizaci běžných úloh ve vašem fondu SQL.  Některé dobré příklady REST najdete v článku [Správa škálovatelnosti pomocí REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

Varianta, v tomto příkladu, přeruší načtený objekt na [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  V důsledku toho je databáze pozastavena. Poslední příkaz zobrazí výsledky.

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

V tomto příkladu se v tomto příkladu načte databáze s názvem "Database02" ze serveru s názvem "Server01", který je součástí skupiny prostředků s názvem "ResourceGroup1". Napředá načtený objekt pro [pokračování – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Všimněte si, že pokud je váš server foo.database.windows.net, použijte v rutinách PowerShellu "foo" jako "servername".
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Další podporované rutiny PowerShellu
Tyto rutiny PowerShellu se podporují s datovým skladem Azure synapse Analytics.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Obnovit – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Pokračovat – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Vybrat – AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Pozastavit – AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Další kroky
Další příklady PowerShellu najdete v tématech:

* [Vytvoření datového skladu pomocí prostředí PowerShell](create-data-warehouse-powershell.md)
* [Obnovení databáze](sql-data-warehouse-restore-database-powershell.md)

Další úkoly, které je možné automatizovat pomocí prostředí PowerShell, najdete v tématu [rutiny Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql). Pro datový sklad Azure synapse Analytics nejsou podporovány všechny rutiny Azure SQL Database.  Seznam úloh, které je možné automatizovat pomocí REST, najdete v tématu [operace pro Azure SQL Database](/rest/api/sql/).
