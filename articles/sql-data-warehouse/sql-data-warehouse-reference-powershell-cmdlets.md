---
title: Rutiny Powershellu pro Azure SQL Data Warehouse
description: Najdete hlavní rutiny Powershellu pro Azure SQL Data Warehouse, včetně postupu pozastavit a obnovit databázi.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ec1f9a8a10f346c4b8e9e8dac364b2687637c204
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317896"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Rutiny prostředí PowerShell a rozhraní REST API pro službu SQL Data Warehouse
Mnoho úkolů správy SQL Data Warehouse je možné spravovat pomocí rutin Azure Powershellu nebo rozhraní REST API.  Níže je několik příkladů toho, jak používat příkazy Powershellu k automatizaci běžných úkolů ve službě SQL Data Warehouse.  Některé dobré příklady REST, najdete v článku [spravovat škálovatelnost s využitím REST][Manage scalability with REST].

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Začínáme s rutinami Azure Powershellu
1. Otevřete Windows PowerShell.
2. Na příkazovém řádku Powershellu, spusťte tyto příkazy pro přihlášení k Azure Resource Manageru a vyberte své předplatné.
   
    ```PowerShell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Pozastavit SQL Data Warehouse příklad
Pozastavit databázi s názvem "Database02" hostovaný na serveru s názvem "Server01."  Server je ve skupině prostředků Azure s názvem "ResourceGroup1."

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Varianta, v tomto příkladu pipes načtený objekt [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase].  V důsledku toho je pozastavena databáze. Poslední příkaz zobrazí výsledky.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Spusťte příklad SQL Data Warehouse
Operace obnovení databáze s názvem "Database02" hostovaný na serveru s názvem "Server01." Na serveru nachází ve skupině prostředků s názvem "ResourceGroup1."

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Varianta, tento příklad načte databázi s názvem "Database02" ze serveru s názvem "Server01", která je obsažena ve skupině prostředků s názvem "ResourceGroup1." Pipes načtený objekt [Resume-AzSqlDatabase][Resume-AzSqlDatabase].

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Poznámka: Pokud server foo.Database.Windows.NET, použijte "foo" jako název_serveru – v rutinách prostředí PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Ostatní podporované rutin prostředí PowerShell
Tyto rutiny Powershellu jsou podporovány službou Azure SQL Data Warehouse.

* [Get-AzSqlDatabase][Get-AzSqlDatabase]
* [Get-AzSqlDeletedDatabaseBackup][Get-AzSqlDeletedDatabaseBackup]
* [Get-AzSqlDatabaseRestorePoints][Get-AzSqlDatabaseRestorePoints]
* [New-AzSqlDatabase][New-AzSqlDatabase]
* [Remove-AzSqlDatabase][Remove-AzSqlDatabase]
* [Restore-AzSqlDatabase][Restore-AzSqlDatabase]
* [Resume-AzSqlDatabase][Resume-AzSqlDatabase]
* [Select-AzSubscription][Select-AzSubscription]
* [Set-AzSqlDatabase][Set-AzSqlDatabase]
* [Suspend-AzSqlDatabase][Suspend-AzSqlDatabase]

## <a name="next-steps"></a>Další postup
Další příklady prostředí PowerShell najdete v tématu:

* [Vytvoření SQL Data Warehouse pomocí prostředí PowerShell][Create a SQL Data Warehouse using PowerShell]
* [Obnovení databáze][Database restore]

Další úlohy, které je možné automatizovat pomocí Powershellu, najdete v části [rutiny služby Azure SQL Database][Azure SQL Database Cmdlets]. Všimněte si, že ne všechny rutiny služby Azure SQL Database jsou podporovány pro službu Azure SQL Data Warehouse.  Seznam úkolů, které je možné automatizovat pomocí REST, naleznete v tématu [operací pro službu Azure SQL Database][Operations for Azure SQL Database].

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Database]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-Azsqldatabase
[Get-AzSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzSqlDeletedDatabaseBackup
[Get-AzSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzSqlDatabaseRestorePoints
[New-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/New-AzSqlDatabase
[Remove-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzSqlDatabase
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Restore-AzSqlDatabase
[Resume-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Resume-AzSqlDatabase
<!-- It appears that Select-AzSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzSqlDatabase
[Suspend-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Suspend-AzSqlDatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
