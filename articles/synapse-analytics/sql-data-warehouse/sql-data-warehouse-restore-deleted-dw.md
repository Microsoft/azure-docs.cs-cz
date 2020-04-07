---
title: Obnovení odstraněného fondu SQL
description: Jak průvodce pro obnovení odstraněného fondu SQL.
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745125"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Obnovení odstraněného fondu SQL pomocí Azure Synapse Analytics

V tomto článku se naučíte obnovit SQL pomocí portálu Azure nebo PowerShellu.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte kapacitu DTU.** Každý fond SQL je hostován sql serverem (například myserver.database.windows.net), který má výchozí kvótu DTU.  Ověřte, zda má server SQL dostatek zbývající kvóty DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o další DTU, naleznete [v tématu Žádost o změnu kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Obnovení odstraněného datového skladu prostřednictvím PowerShellu

Chcete-li obnovit odstraněný fond SQL, použijte rutinu [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Pokud byl odstraněn také odpovídající logický server, nelze tento datový sklad obnovit.

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otevřete PowerShell.
3. Připojte se ke svému účtu Azure a uveďte všechna předplatná přidružená k vašemu účtu.
4. Vyberte předplatné, které obsahuje odstraněný datový sklad, který má být obnoven.
5. Získejte konkrétní odstraněný datový sklad.
6. Obnovení odstraněného datového skladu
    1. Chcete-li obnovit odstraněný datový sklad SQL na jiný logický server, zadejte jiný název logického serveru.  Tento logický server může být také v jiné skupině prostředků a oblasti.
    1. Chcete-li obnovit jiné předplatné, přesuňte logický server na jiné předplatné pomocí tlačítka [Přesunout.](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal)
7. Ověřte, zda je obnovený datový sklad online.
8. Po dokončení obnovení můžete nakonfigurovat obnovený datový sklad tak, že [nakonfigurujete databázi po obnovení](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Obnovení odstraněné databáze pomocí webu Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Přejděte na sql server, na který byl umístěn odstraněný datový sklad.
3. V obsahu vyberte ikonu **Odstraněná databáze.**

    ![Odstraněné databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Vyberte odstraněný datový sklad SQL, který chcete obnovit.

    ![Výběr možnosti Odstraněné databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Zadejte nový **název databáze** a klepněte na **OK.**

    ![Zadat název databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení existujícího fondu SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení z fondu SQL geografické zálohování](sql-data-warehouse-restore-from-geo-backup.md)
