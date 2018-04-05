---
title: Obnovit Azure SQL Data Warehouse (PowerShell) | Microsoft Docs
description: Prostředí PowerShell úlohy pro obnovení Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.assetid: ac62f154-c8b0-4c33-9c42-f480808aa1d2
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 02/27/2018
ms.author: barbkess
ms.openlocfilehash: 6c4ed8415e60e32b7813bc27dd9aff620ddc65cc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Obnovit Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Přehled][Overview]
> * [Portál][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

V tomto článku se dozvíte, jak obnovit Azure SQL Data Warehouse pomocí prostředí PowerShell.

## <a name="before-you-begin"></a>Než začnete
**Ověření vaší DTU kapacity.** Každý datový sklad SQL je hostitelem serveru SQL (např. myserver.database.windows.net), který má kvóty DTU.  Před obnovením SQL Data Warehouse, ověřte, zda serveru SQL server má dostatek zbývající kvóty DTU pro databáze obnovena. Informace o výpočtu DTU potřeby nebo požádejte o další DTU najdete v tématu [žádosti o změnu kvóty DTU][Request a DTU quota change].

### <a name="install-powershell"></a>Instalace PowerShellu
Abyste mohli používat Azure PowerShell s SQL Data Warehouse, musíte nainstalovat Azure PowerShell verze 1.0 nebo novější.  Vaše verze můžete zkontrolovat spuštěním **Get-Module - ListAvailable-AzureRM název**.  Nejnovější verzi můžete nainstalovat z [instalačního programu webové platformy Microsoft][Microsoft Web Platform Installer].  Další informace o instalaci nejnovější verze najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell][How to install and configure Azure PowerShell].

## <a name="restore-an-active-or-paused-database"></a>Obnovit databázi active nebo pozastavena
K obnovení databázi z použití snímku [obnovení-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] rutiny prostředí PowerShell.

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a seznamu všechna předplatná spojená s vaším účtem.
3. Vyberte odběr, který obsahuje databázi obnovit.
4. Zobrazí seznam bodů obnovení pro databázi.
5. Vyberte bod požadované obnovení pomocí RestorePointCreationDate.
6. Obnovení databáze do bodu požadované obnovení.
7. Ověřte, že obnovené databáze je online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené databáze pomocí následujících [nakonfigurovat databázi po obnovení][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Obnovení odstraněné databáze
Chcete-li obnovit odstraněnou databázi, použijte [obnovení-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] rutiny.

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a seznamu všechna předplatná spojená s vaším účtem.
3. Vyberte odběr, který obsahuje obnovit odstraněnou databázi.
4. Získejte konkrétní odstraněnou databázi.
5. Obnovte odstraněnou databázi.
6. Ověřte, že obnovené databáze je online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Po dokončení obnovení můžete nakonfigurovat obnovené databáze pomocí následujících [nakonfigurovat databázi po obnovení][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Obnovení z Azure geografické oblasti
Chcete-li obnovit databázi, použijte [obnovení-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] rutiny.

> [!NOTE]
> Můžete provést geografické obnovení do optimalizovaný pro výpočetní výkon vrstvě! Uděláte to tak, zadejte optimalizovaný pro výpočetní ServiceObjectiveName jako volitelný parametr. 
>
> 

1. Otevřete Windows PowerShell.
2. Připojte se ke svému účtu Azure a seznamu všechna předplatná spojená s vaším účtem.
3. Vyberte odběr, který obsahuje databázi obnovit.
4. Získáte databázi, kterou chcete obnovit.
5. Vytvořte žádost o obnovení pro databázi.
6. Zkontrolujte stav databáze geografické obnovení.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Konfigurace databáze po dokončení obnovení najdete v tématu [nakonfigurovat databázi po obnovení][Configure your database after recovery].
> 
> 

Pokud zdrojové databáze je povolené šifrování TDE, budou obnovené databáze povolené šifrování TDE.

## <a name="next-steps"></a>Další postup
Další informace o funkcích kontinuity obchodních edice Azure SQL Database, přečtěte si [Azure SQL Database obchodní kontinuity přehled][Azure SQL Database business continuity overview].

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
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
