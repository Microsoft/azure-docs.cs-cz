---
title: Obnovení služby Azure SQL Data Warehouse (PowerShell) | Dokumentace Microsoftu
description: Úlohy prostředí PowerShell pro obnovení Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6324eb11b334fd6a00e30d6f2fc6d1bec3f7a82c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870819"
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Obnovení služby Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Přehled][Overview]
> * [Portál][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

V tomto článku se dozvíte, jak obnovit službu Azure SQL Data Warehouse pomocí prostředí PowerShell.

## <a name="before-you-begin"></a>Před zahájením

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ověřte kapacitu jednotek DTU.** Každý datový sklad SQL je hostitelem SQL serveru (např. myserver.database.windows.net), který má výchozí kvóty DTU.  Předtím, než bude možné obnovit SQL Data Warehouse, ověřte, že serveru SQL server má dostatek zbývající kvóta DTU databáze obnovena. Pokud chcete zjistit, jak chcete-li vypočítat potřebné DTU, nebo požádat o další DTU, přečtěte si téma [žádost o změnu kvóty DTU][Request a DTU quota change].

### <a name="install-powershell"></a>Instalace PowerShellu
Chcete-li používat Azure PowerShell s využitím SQL Data Warehouse, je potřeba nainstalovat Azure PowerShell.  Verzi zjistíte spuštěním **Get-Module - ListAvailable-Name Az**.  Další informace o instalaci nejnovější verze najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Obnovit databázi aktivní nebo pozastavena
Obnovení databáze z použití snímku [obnovení AzSqlDatabase] [ Restore-AzSqlDatabase] rutiny Powershellu.

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a vypsat všechna předplatná spojená s vaším účtem.
3. Vyberte předplatné, která obsahuje databázi obnovit.
4. Vypsat body obnovení pro databázi.
5. Vyberte bod obnovení požadovaných pomocí RestorePointCreationDate.
6. Obnovení databáze do místa požadovaného obnovení.
7. Ověřte, že obnovené databáze online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené databáze pomocí následujících [nakonfigurovat svou databázi po obnovení][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze
Chcete-li obnovit odstraněnou databázi, použijte [obnovení AzSqlDatabase] [ Restore-AzSqlDatabase] rutiny.

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a vypsat všechna předplatná spojená s vaším účtem.
3. Vyberte předplatné, které obsahuje odstraněnou databázi obnovit.
4. Získání konkrétní odstraněnou databázi.
5. Obnovení odstraněné databáze.
6. Ověřte, že obnovené databáze online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené databáze pomocí následujících [nakonfigurovat svou databázi po obnovení][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Obnovení z Azure geografické oblasti
Chcete-li obnovit databázi, použijte [obnovení AzSqlDatabase] [ Restore-AzSqlDatabase] rutiny.

> [!NOTE]
> Můžete provést geografické obnovení do optimalizováno pro výpočetní úroveň výkonu! Uděláte to tak, zadejte optimalizováno pro výpočetní ServiceObjectiveName jako volitelný parametr. 
>
> 

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a vypsat všechna předplatná spojená s vaším účtem.
3. Vyberte předplatné, která obsahuje databázi obnovit.
4. Získáte databáze, kterou chcete obnovit.
5. Vytvořte žádost o obnovení pro databázi.
6. Ověřte stav databázi geograficky obnovit.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Po dokončení obnovení konfigurace vaší databáze, najdete v článku [nakonfigurovat svou databázi po obnovení][Configure your database after recovery].
> 
> 

Pokud zdrojová databáze je povolené šifrování TDE, bude obnovenou databázi povoleno TDE.

## <a name="next-steps"></a>Další postup
Další informace o obchodní kontinuity podnikových procesů funkce edice Azure SQL Database, přečtěte si prosím [přehled zajištění provozní kontinuity podnikání Azure SQL Database][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
