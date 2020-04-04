---
title: Obnovení stávajícího datového skladu
description: Návod pro obnovení existujícího fondu SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e1d0c5cd850fa0dbacf104d3fcd72d83007df878
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632251"
---
# <a name="restore-an-existing-sql-pool"></a>Obnovení existujícího fondu SQL

V tomto článku se dozvíte, jak obnovit existující fond SQL v Azure Synapse Analytics pomocí portálu Azure a PowerShellu.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte kapacitu DTU.** Každý fond je hostován sql server (například myserver.database.windows.net), který má výchozí kvótu DTU. Ověřte, zda má server SQL dostatek zbývající kvóty DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o další DTU, naleznete [v tématu Žádost o změnu kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Než začnete

1. Nezapomeňte [nainstalovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Mít existující bod obnovení, ze kterého chcete obnovit. Pokud chcete vytvořit nové obnovení, podívejte [se do kurzu k vytvoření nového uživatelem definovaného bodu obnovení](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Obnovení existujícího fondu SQL prostřednictvím prostředí PowerShell

Chcete-li obnovit existující fond SQL z bodu obnovení, použijte rutinu [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell.

1. Otevřete PowerShell.

2. Připojte se ke svému účtu Azure a uveďte všechna předplatná přidružená k vašemu účtu.

3. Vyberte odběr, který obsahuje databázi, která má být obnovena.

4. Seznam bodů obnovení pro fond SQL.

5. Vyberte požadovaný bod obnovení pomocí RestorePointCreationDate.

6. Obnovte fond SQL do požadovaného bodu obnovení pomocí rutiny [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell.
        1. Chcete-li obnovit fond SQL na jiný logický server, nezapomeňte zadat jiný název logického serveru.  Tento logický server může být také v jiné skupině prostředků a oblasti.
        2. Chcete-li obnovit jiné předplatné, použijte tlačítko Přesunout k přesunutí logického serveru na jiné předplatné.

7. Ověřte, zda je obnovený fond SQL online.

8. Po dokončení obnovení můžete nakonfigurovat obnovený fond SQL tak, že [nakonfigurujete databázi po obnovení](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Obnovení existujícího fondu SQL prostřednictvím portálu Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Přejděte do fondu SQL, ze kterého chcete obnovit.
3. V horní části okna Přehled vyberte **Obnovit**.

    ![ Obnovení – přehled](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Vyberte buď **body automatického obnovení,** nebo **uživatelem definované body obnovení**. Pokud fond SQL nemá žádné body automatického obnovení, počkejte několik hodin nebo vytvořte uživatelem definovaný bod obnovení před obnovením. V části Uživatelem definované body obnovení vyberte existující body nebo vytvořte nový. V **systému Server**můžete vybrat logický server v jiné skupině prostředků a oblasti nebo vytvořit nový. Po zadání všech parametrů klepněte na tlačítko **Zkontrolovat + Obnovit**.

    ![Automatické body obnovení](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení odstraněného fondu SQL](sql-data-warehouse-restore-deleted-dw.md)
- [Obnovení z fondu SQL geografické zálohování](sql-data-warehouse-restore-from-geo-backup.md)
