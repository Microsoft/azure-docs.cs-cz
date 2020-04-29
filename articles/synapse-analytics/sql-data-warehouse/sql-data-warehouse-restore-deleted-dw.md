---
title: Obnovení odstraněného fondu SQL
description: Návod k obnovení odstraněného fondu SQL.
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
ms.openlocfilehash: d2e2fdb181b553d330368b043b75159e211dd0d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745125"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Obnovení odstraněného fondu SQL pomocí Azure synapse Analytics

V tomto článku se naučíte, jak obnovit SQL pomocí Azure Portal nebo PowerShellu.

## <a name="before-you-begin"></a>Před zahájením

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý fond SQL je hostovaný SQL serverem (například myserver.database.windows.net), který má výchozí kvótu DTU.  Ověřte, zda má systém SQL Server dostatek zbývajících kvót DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Obnovení odstraněného datového skladu prostřednictvím PowerShellu

K obnovení odstraněného fondu SQL použijte rutinu [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Pokud byl odstraněn i odpovídající logický Server, nemůžete tento datový sklad obnovit.

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otevřete PowerShell.
3. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.
4. Vyberte předplatné, které obsahuje odstraněný datový sklad, který se má obnovit.
5. Získat konkrétní odstraněný datový sklad.
6. Obnovit odstraněný datový sklad
    1. Chcete-li obnovit odstraněné SQL Data Warehouse na jiný logický Server, nezapomeňte zadat jiný název logického serveru.  Tento logický Server může být taky v jiné skupině prostředků a oblasti.
    1. K obnovení do jiného předplatného použijte tlačítko [přesunout](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) k přesunutí logického serveru do jiného předplatného.
7. Ověřte, že je obnovený datový sklad online.
8. Po dokončení obnovení můžete po obnovení nakonfigurovat obnovený datový sklad pomocí [Konfigurace databáze](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Obnovení odstraněné databáze pomocí Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přejděte na SQL Server, na kterém jste odstraněné datový sklad hostoval.
3. V obsahu vyberte ikonu **odstraněné databáze** .

    ![Odstraněné databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Vyberte odstraněný SQL Data Warehouse, kterou chcete obnovit.

    ![Výběr možnosti Odstraněné databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Zadejte nový **název databáze** a klikněte na **OK** .

    ![Zadat název databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Další kroky

- [Obnovit existující fond SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení z fondu SQL geografického zálohování](sql-data-warehouse-restore-from-geo-backup.md)
