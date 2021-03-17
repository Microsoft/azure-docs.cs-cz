---
title: Obnovení existujícího vyhrazeného fondu SQL (dřív SQL DW)
description: Návod pro obnovení existujícího vyhrazeného fondu SQL ve službě Azure synapse Analytics
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591986"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Obnovení existujícího vyhrazeného fondu SQL (dřív SQL DW)

V tomto článku se dozvíte, jak obnovit existující vyhrazený fond SQL (dřív SQL DW) pomocí Azure Portal a PowerShellu.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý fond je hostovaný [logickým SQL serverem](../../azure-sql/database/logical-servers.md) (například MyServer.Database.Windows.NET), který má výchozí kvótu DTU. Ověřte, že server má pro obnovenou databázi dostatečný počet zbývajících kvót DTU. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Před zahájením

1. Nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Máte existující bod obnovení, ze kterého chcete obnovit. Pokud chcete vytvořit nové obnovení, přečtěte si [kurz vytvoření nového bodu obnovení definovaného uživatelem](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>Obnovení existujícího vyhrazeného fondu SQL (dřív SQL DW) prostřednictvím PowerShellu

Pokud chcete obnovit existující vyhrazený fond SQL (dřív SQL DW) z bodu obnovení, použijte rutinu [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) prostředí PowerShell.

1. Otevřete PowerShell.

2. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.

3. Vyberte předplatné, které obsahuje databázi, kterou chcete obnovit.

4. Vypíše body obnovení pro vyhrazený fond SQL (dříve SQL DW).

5. Vyberte požadovaný bod obnovení pomocí RestorePointCreationDate.

6. Obnovte vyhrazený fond SQL (dřív SQL DW) do požadovaného bodu obnovení pomocí rutiny [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) prostředí PowerShell.

    1. Chcete-li obnovit vyhrazený fond SQL (dříve SQL DW) na jiný server, nezapomeňte zadat jiný název serveru.  Tento server může být také v jiné skupině prostředků a oblasti.
    2. K obnovení do jiného předplatného použijte tlačítko přesunout a přesuňte server do jiného předplatného.

7. Ověřte, jestli je obnovený vyhrazený fond SQL (dřív SQL DW) online.

8. Po dokončení obnovení můžete nakonfigurovat obnovený vyhrazený fond SQL (dřív SQL DW), a to [po obnovení po obnovení nakonfigurovat databázi](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>Obnovte existující vyhrazený fond SQL (dřív SQL DW) prostřednictvím Azure Portal

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Přejděte na vyhrazené, ze kterého chcete obnovit.
3. V horní části okna Přehled vyberte **obnovit**.

    ![ Obnovení – přehled](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Vyberte buď **body automatického obnovení** nebo **body obnovení definované uživatelem**. Pokud vyhrazený fond SQL (dříve SQL DW) nemá žádné automatické body obnovení, počkejte pár hodin nebo před obnovením vytvořte uživatelem definovaný bod obnovení. U User-Defined bodů obnovení vyberte existující nebo vytvořte novou. V případě **serveru** můžete vybrat server v jiné skupině prostředků a oblasti nebo vytvořit nový. Po zadání všech parametrů klikněte na **zkontrolovat + obnovit**.

    ![Automatické body obnovení](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení odstraněného vyhrazeného fondu SQL (dřív SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
- [Obnovení z vyhrazeného fondu SQL geografického zálohování (dříve SQL DW)](sql-data-warehouse-restore-from-geo-backup.md)
