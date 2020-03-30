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
ms.openlocfilehash: 5f0432cafee07dbed071d24aa8c24ee9b2176967
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350175"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Obnovení odstraněného fondu SQL pomocí Azure Synapse Analytics

V tomto článku se naučíte obnovit SQL pomocí portálu Azure nebo PowerShellu.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte kapacitu DTU.** Každý fond SQL je hostován sql serverem (například myserver.database.windows.net), který má výchozí kvótu DTU.  Ověřte, zda má server SQL dostatek zbývající kvóty DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o další DTU, naleznete [v tématu Žádost o změnu kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Obnovení odstraněného datového skladu prostřednictvím PowerShellu

Chcete-li obnovit odstraněný fond SQL, použijte rutinu [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) Pokud byl odstraněn také odpovídající logický server, nelze tento datový sklad obnovit.

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Otevřete PowerShell.
3. Připojte se ke svému účtu Azure a uveďte všechna předplatná přidružená k vašemu účtu.
4. Vyberte předplatné, které obsahuje odstraněný datový sklad, který má být obnoven.
5. Získejte konkrétní odstraněný datový sklad.
6. Obnovení odstraněného datového skladu
    1. Chcete-li obnovit odstraněný datový sklad SQL na jiný logický server, zadejte jiný název logického serveru.  Tento logický server může být také v jiné skupině prostředků a oblasti.
    1. Chcete-li obnovit jiné předplatné, přesuňte logický server na jiné předplatné pomocí tlačítka [Přesunout.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal)
1. Ověřte, zda je obnovený datový sklad online.
1. Po dokončení obnovení můžete nakonfigurovat obnovený datový sklad tak, že [nakonfigurujete databázi po obnovení](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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