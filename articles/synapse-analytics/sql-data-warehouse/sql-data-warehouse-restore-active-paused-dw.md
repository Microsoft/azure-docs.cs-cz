---
title: Obnovení stávajícího datového skladu
description: Návod pro obnovení existujícího fondu SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 62db1acd704aeb563c5644d7c963c65a3abdfd0c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289646"
---
# <a name="restore-an-existing-sql-pool"></a>Obnovit existující fond SQL

V tomto článku se dozvíte, jak obnovit existující fond SQL ve službě Azure synapse Analytics pomocí Azure Portal a PowerShellu.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý fond je hostovaný [logickým SQL serverem](../../azure-sql/database/logical-servers.md) (například MyServer.Database.Windows.NET), který má výchozí kvótu DTU. Ověřte, že server má pro obnovenou databázi dostatečný počet zbývajících kvót DTU. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Než začnete

1. Nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Máte existující bod obnovení, ze kterého chcete obnovit. Pokud chcete vytvořit nové obnovení, přečtěte si [kurz vytvoření nového bodu obnovení definovaného uživatelem](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Obnovení existujícího fondu SQL pomocí PowerShellu

Pokud chcete obnovit existující fond SQL z bodu obnovení, použijte rutinu [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) prostředí PowerShell.

1. Otevřete PowerShell.

2. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.

3. Vyberte předplatné, které obsahuje databázi, kterou chcete obnovit.

4. Vypíše body obnovení pro fond SQL.

5. Vyberte požadovaný bod obnovení pomocí RestorePointCreationDate.

6. Obnovte fond SQL na požadovaný bod obnovení pomocí rutiny [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) prostředí PowerShell.

    1. Chcete-li obnovit fond SQL na jiný server, nezapomeňte zadat jiný název serveru.  Tento server může být také v jiné skupině prostředků a oblasti.
    2. K obnovení do jiného předplatného použijte tlačítko přesunout a přesuňte server do jiného předplatného.

7. Ověřte, zda je obnovený fond SQL online.

8. Po dokončení obnovení můžete po obnovení nakonfigurovat obnovený fond SQL pomocí [Konfigurace databáze](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Obnovte stávající fond SQL prostřednictvím Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte do fondu SQL, ze kterého chcete obnovit.
3. V horní části okna Přehled vyberte **obnovit**.

    ![ Obnovení – přehled](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Vyberte buď **body automatického obnovení** nebo **body obnovení definované uživatelem**. Pokud fond SQL neobsahuje žádné body automatického obnovení, počkejte několik hodin nebo před obnovením vytvořte uživatelem definovaný bod obnovení. V případě uživatelem definovaných bodů obnovení vyberte existující nebo vytvořte novou. V případě **serveru**můžete vybrat server v jiné skupině prostředků a oblasti nebo vytvořit nový. Po zadání všech parametrů klikněte na **zkontrolovat + obnovit**.

    ![Automatické body obnovení](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení odstraněného fondu SQL](sql-data-warehouse-restore-deleted-dw.md)
- [Obnovení z fondu SQL geografického zálohování](sql-data-warehouse-restore-from-geo-backup.md)
