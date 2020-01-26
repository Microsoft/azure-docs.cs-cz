---
title: Obnovení stávajícího datového skladu
description: Návod, jak obnovit existující Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d0bcf9ca6373984989d24efd2af4ffbbb19c5548
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759678"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Obnovit existující Azure SQL Data Warehouse

V tomto článku se naučíte obnovit existující SQL Data Warehouse prostřednictvím Azure Portal a PowerShellu:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý SQL Data Warehouse hostuje SQL Server (například myserver.database.windows.net), který má výchozí kvótu DTU. Ověřte, zda má SQL Server dostatek zbývajících kvót DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Než začnete

1. Nezapomeňte [nainstalovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Máte existující bod obnovení, ze kterého chcete obnovit. Pokud chcete vytvořit nové obnovení, přečtěte si [kurz vytvoření nového bodu obnovení definovaného uživatelem](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Obnovení existujícího datového skladu prostřednictvím PowerShellu

Chcete-li obnovit existující datový sklad z bodu obnovení, použijte rutinu [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) prostředí PowerShell.

1. Otevřete PowerShell.

2. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.

3. Vyberte předplatné, které obsahuje databázi, kterou chcete obnovit.

4. Seznam bodů obnovení datového skladu.

5. Vyberte požadovaný bod obnovení pomocí RestorePointCreationDate.

6. Obnovte datový sklad na požadovaný bod obnovení pomocí rutiny [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) prostředí PowerShell.
        1. Chcete-li obnovit SQL Data Warehouse na jiný logický Server, nezapomeňte zadat jiný název logického serveru.  Tento logický Server může být taky v jiné skupině prostředků a oblasti.
        2. K obnovení do jiného předplatného použijte tlačítko přesunout, aby se logický Server přesunul do jiného předplatného.

7. Ověřte, že je obnovený datový sklad online.

8. Po dokončení obnovení můžete po obnovení nakonfigurovat obnovený datový sklad pomocí [Konfigurace databáze](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Obnovit existující datový sklad pomocí Azure Portal

1. Přihlaste se k [Portálu Azure](https://portal.azure.com/).
2. Přejděte na SQL Data Warehouse, ze kterého chcete obnovit.
3. V horní části okna Přehled vyberte **obnovit**.

    ![ Obnovení – přehled](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Vyberte buď **body automatického obnovení** nebo **body obnovení definované uživatelem**. Pokud datový sklad neobsahuje žádné automatické body obnovení, počkejte několik hodin nebo před obnovením vytvořte uživatelem definovaný bod obnovení. V případě uživatelem definovaných bodů obnovení vyberte existující nebo vytvořte novou. V případě **serveru**můžete vybrat logický Server v jiné skupině prostředků a oblasti nebo vytvořit nový. Po zadání všech parametrů klikněte na **zkontrolovat + obnovit**.

    ![Automatické body obnovení](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Další kroky
- [Obnovení odstraněného datového skladu](sql-data-warehouse-restore-deleted-dw.md)
- [Obnovení z geograficky záložního datového skladu](sql-data-warehouse-restore-from-geo-backup.md)

 