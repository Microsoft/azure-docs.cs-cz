---
title: Obnovení datového skladu ze geografické zálohy
description: Návod pro geograficky obnovení fondu SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7e0980a9142dc966916d5a4df898ea53b0ddeae5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745078"
---
# <a name="geo-restore-for-sql-pool"></a>Geografické obnovení pro fond SQL

V tomto článku se naučíte obnovit fond SQL z geografické zálohy prostřednictvím portálu Azure a PowerShellu.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte kapacitu DTU.** Každý fond SQL je hostován sql serverem (například myserver.database.windows.net), který má výchozí kvótu DTU. Ověřte, zda má server SQL dostatek zbývající kvóty DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o další DTU, naleznete [v tématu Žádost o změnu kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Obnovení ze zeměpisné oblasti Azure prostřednictvím PowerShellu

Chcete-li obnovit z geografické zálohy, použijte [Rutina Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

> [!NOTE]
> Můžete provést geo-obnovení Gen2! Chcete-li tak učinit, zadejte Gen2 ServiceObjectiveName (např. DW1000**c**) jako volitelný parametr.
>

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otevřete PowerShell.
3. Připojte se ke svému účtu Azure a uveďte všechna předplatná přidružená k vašemu účtu.
4. Vyberte předplatné, které obsahuje datový sklad, který má být obnoven.
5. Získejte datový sklad, který chcete obnovit.
6. Vytvořte požadavek na obnovení pro datový sklad.
7. Ověřte stav geograficky obnoveného datového skladu.
8. Pokud chcete nakonfigurovat datový sklad po dokončení obnovení, přečtěte si informace [o konfiguraci databáze po obnovení]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

Obnovená databáze bude povolena tde, pokud je zdrojová databáze povolena TDE.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Obnovení ze zeměpisné oblasti Azure prostřednictvím portálu Azure

Chcete-li obnovit fond SQL z geografické zálohy, postupujte podle následujících kroků:

1. Přihlaste se ke svému účtu [na Portálu Azure.](https://portal.azure.com/)
2. Klepněte na tlačítko **+ Vytvořit zdroj**.

   ![Nové DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Klikněte na **databáze** a potom na **Azure Synapse Analytics (dříve SQL DW) **.

   ![Nové DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Vyplňte požadované informace na kartě **Základy** a klepněte na tlačítko **Další: Další nastavení**.

   ![Základy](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. V **části Použít parametr existujících dat** vyberte Možnost **Zálohování** a z možností posunu dolů vyberte příslušnou zálohu. Klepněte na tlačítko **Revize + Vytvořit**.

   ![zálohování](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Po obnovení datového skladu zkontrolujte, zda je **stav** online.

## <a name="next-steps"></a>Další kroky

- [Obnovení existujícího fondu SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení odstraněného fondu SQL](sql-data-warehouse-restore-deleted-dw.md)
