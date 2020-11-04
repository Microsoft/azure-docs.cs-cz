---
title: Obnovení odstraněného vyhrazeného fondu SQL
description: Příručka k obnovení odstraněného fondu SQL ve službě Azure synapse Analytics.
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
ms.openlocfilehash: 29edf6ebd451bf05fe24249eeacb416a70001d56
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313578"
---
# <a name="restore-a-deleted-dedicated-sql-pool-in-azure-synapse-analytics"></a>Obnovení odstraněného vyhrazeného fondu SQL ve službě Azure synapse Analytics

V tomto článku se naučíte obnovit vyhrazený fond SQL pomocí Azure Portal nebo PowerShellu.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý vyhrazený fond SQL je hostovaný [logickým SQL serverem](../../azure-sql/database/logical-servers.md) (například MyServer.Database.Windows.NET), který má výchozí kvótu DTU.  Ověřte, že server má pro obnovenou databázi dostatečný počet zbývajících kvót DTU. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Obnovení odstraněného datového skladu prostřednictvím PowerShellu

K obnovení odstraněného vyhrazeného fondu SQL použijte rutinu [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Pokud byl odpovídající server odstraněn také, nemůžete tento datový sklad obnovit.

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otevřete PowerShell.
3. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.
4. Vyberte předplatné, které obsahuje odstraněný vyhrazený fond SQL, který se má obnovit.
5. Získat konkrétní odstraněný datový sklad.
6. Obnovení odstraněného vyhrazeného fondu SQL
    1. Chcete-li obnovit odstraněný vyhrazený fond SQL na jiný server, nezapomeňte zadat jiný název serveru.  Tento server může být také v jiné skupině prostředků a oblasti.
    1. K obnovení do jiného předplatného použijte tlačítko [přesunout](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) k přesunutí serveru do jiného předplatného.
7. Ověřte, že je obnovený datový sklad online.
8. Po dokončení obnovení můžete po obnovení nakonfigurovat obnovený datový sklad pomocí [Konfigurace databáze](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Obnovení odstraněné databáze pomocí Azure Portal

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Přejděte na server, na kterém byl uložen odstraněný datový sklad.
3. V obsahu vyberte ikonu **odstraněné databáze** .

    ![Odstraněné databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Vyberte odstraněnou analýzu Azure synapse, kterou chcete obnovit.

    ![Výběr možnosti Odstraněné databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Zadejte nový **název databáze** a klikněte na **OK** .

    ![Zadat název databáze](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení existujícího vyhrazeného fondu SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení z vyhrazeného fondu SQL geografického zálohování](sql-data-warehouse-restore-from-geo-backup.md)
