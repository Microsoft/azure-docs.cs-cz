---
title: Rutiny prostředí PowerShell
description: Najděte si hlavní rutiny PowerShellu pro Azure SQL Data Warehouse, včetně toho, jak pozastavit a obnovit databázi.
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
ms.openlocfilehash: b36a64bb82449ace7acc1de0b3c2bc7c5efebe70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685553"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Rutiny prostředí PowerShell a rozhraní REST API pro SQL Data Warehouse
Mnohé úlohy správy SQL Data Warehouse můžete spravovat pomocí rutin Azure PowerShell nebo rozhraní REST API.  Níže uvádíme několik příkladů použití příkazů PowerShellu k automatizaci běžných úloh v SQL Data Warehouse.  Některé dobré příklady REST najdete v článku [Správa škálovatelnosti pomocí REST][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Začínáme s rutinami Azure PowerShell
1. Otevřete Windows PowerShell.
2. Spuštěním těchto příkazů na příkazovém řádku PowerShellu se přihlaste k Azure Resource Manager a vyberte své předplatné.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Příklad pozastavení SQL Data Warehouse
Pozastavení databáze s názvem "Database02", která je hostována na serveru s názvem "Server01".  Server je ve skupině prostředků Azure s názvem "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Varianta, v tomto příkladu, přeruší načtený objekt na [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase].  V důsledku toho je databáze pozastavena. Poslední příkaz zobrazí výsledky.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Příklad spuštění SQL Data Warehouse
Operace obnovení databáze s názvem "Database02", která je hostována na serveru s názvem "Server01". Server je součástí skupiny prostředků s názvem "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

V tomto příkladu se v tomto příkladu načte databáze s názvem "Database02" ze serveru s názvem "Server01", který je součástí skupiny prostředků s názvem "ResourceGroup1". Napředá načtený objekt pro [pokračování – AzSqlDatabase][Resume-AzSqlDatabase].

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Všimněte si, že pokud je váš server foo.database.windows.net, použijte v rutinách PowerShellu "foo" jako "servername".
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Další podporované rutiny PowerShellu
Tyto rutiny PowerShellu jsou podporované Azure SQL Data Warehouse.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoint][Get-AzSqlDatabaseRestorePoint]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Obnovit – AzSqlDatabase][Restore-AzSqlDatabase]
* [Pokračovat – AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Pozastavit – AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Další kroky
Další příklady PowerShellu najdete v tématech:

* [Vytvoření SQL Data Warehouse pomocí prostředí PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Obnovení databáze][Database restore]

Další úkoly, které je možné automatizovat pomocí prostředí PowerShell, najdete v tématu [rutiny Azure SQL Database][Azure SQL Database Cmdlets]. Všimněte si, že nejsou podporovány všechny rutiny Azure SQL Database pro Azure SQL Data Warehouse.  Seznam úloh, které je možné automatizovat pomocí REST, najdete v tématu [operace pro Azure SQL Database][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/az.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup
[Get-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
