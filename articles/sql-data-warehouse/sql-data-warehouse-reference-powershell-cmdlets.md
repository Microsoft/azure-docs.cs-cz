---
title: Rutiny Powershellu pro Azure SQL Data Warehouse
description: Najdete hlavní rutiny Powershellu pro Azure SQL Data Warehouse, včetně postupu pozastavit a obnovit databázi.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 222bc8ee15fdc8802dacd5a5b74cfd84961aa397
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300749"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Rutiny prostředí PowerShell a rozhraní REST API pro službu SQL Data Warehouse
Mnoho úkolů správy SQL Data Warehouse je možné spravovat pomocí rutin Azure Powershellu nebo rozhraní REST API.  Níže je několik příkladů toho, jak používat příkazy Powershellu k automatizaci běžných úkolů ve službě SQL Data Warehouse.  Některé dobré příklady REST, najdete v článku [spravovat škálovatelnost s využitím REST][Manage scalability with REST].

> [!NOTE]
> Chcete-li používat Azure PowerShell s využitím SQL Data Warehouse, je třeba Azure PowerShell verze 1.0.3 nebo novější.  Verzi zjistíte spuštěním **Get-Module - ListAvailable-Name Azure**.  Nejnovější verzi si můžete nainstalovat z [instalačního programu webové platformy Microsoft][Microsoft Web Platform Installer].  Další informace o instalaci nejnovější verze najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell][How to install and configure Azure PowerShell].
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Začínáme s rutinami Azure Powershellu
1. Otevřete Windows PowerShell.
2. Na příkazovém řádku Powershellu, spusťte tyto příkazy pro přihlášení k Azure Resource Manageru a vyberte své předplatné.
   
    ```PowerShell
    Connect-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Pozastavit SQL Data Warehouse příklad
Pozastavit databázi s názvem "Database02" hostovaný na serveru s názvem "Server01."  Server je ve skupině prostředků Azure s názvem "ResourceGroup1."

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Varianta, v tomto příkladu pipes načtený objekt [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase].  V důsledku toho je pozastavena databáze. Poslední příkaz zobrazí výsledky.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Spusťte příklad SQL Data Warehouse
Operace obnovení databáze s názvem "Database02" hostovaný na serveru s názvem "Server01." Na serveru nachází ve skupině prostředků s názvem "ResourceGroup1."

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Varianta, tento příklad načte databázi s názvem "Database02" ze serveru s názvem "Server01", která je obsažena ve skupině prostředků s názvem "ResourceGroup1." Pipes načtený objekt [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> Poznámka: Pokud server foo.Database.Windows.NET, použijte "foo" jako název_serveru – v rutinách prostředí PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Ostatní podporované rutin prostředí PowerShell
Tyto rutiny Powershellu jsou podporovány službou Azure SQL Data Warehouse.

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [Nový AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [Select-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>Další postup
Další příklady prostředí PowerShell najdete v tématu:

* [Vytvoření SQL Data Warehouse pomocí prostředí PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Obnovení databáze][Database restore]

Další úlohy, které je možné automatizovat pomocí Powershellu, najdete v části [rutiny služby Azure SQL Database][Azure SQL Database Cmdlets]. Všimněte si, že ne všechny rutiny služby Azure SQL Database jsou podporovány pro službu Azure SQL Data Warehouse.  Seznam úkolů, které je možné automatizovat pomocí REST, naleznete v tématu [operací s databází Azure SQL Database][Operations for Azure SQL Databases].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Databases]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase
[Get-AzureRmSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDeletedDatabaseBackup
[Get-AzureRmSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDatabaseRestorePoints
[New-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/New-AzureRmSqlDatabase
[Remove-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlDatabase
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Restore-AzureRmSqlDatabase
[Resume-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Resume-AzureRmSqlDatabase
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlDatabase
[Suspend-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Suspend-AzureRmSqlDatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
