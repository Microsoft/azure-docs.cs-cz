---
title: Obnovení vyhrazeného fondu SQL z geografické zálohy
description: Návod pro geografickou obnovu vyhrazeného fondu SQL ve službě Azure synapse Analytics
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
ms.openlocfilehash: 4683bd84873506483209f4a0eb3751a1b163ed48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96449849"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Geografické obnovení vyhrazeného fondu SQL ve službě Azure synapse Analytics

V tomto článku se dozvíte, jak obnovit vyhrazený fond SQL (dřív SQL DW) z geografické zálohy prostřednictvím Azure Portal a PowerShellu.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý vyhrazený fond SQL (dřív SQL DW) je hostovaný [logickým SQL serverem](../../azure-sql/database/logical-servers.md) (například MyServer.Database.Windows.NET), který má výchozí kvótu DTU. Ověřte, zda má systém SQL Server dostatek zbývajících kvót DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Obnovení z geografické oblasti Azure prostřednictvím PowerShellu

K obnovení z geografické zálohy použijte rutinu [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> K Gen2 můžete použít geografické obnovení. Provedete to tak, že jako volitelný parametr zadáte Gen2 ServiceObjectiveName (např. DW1000 **c**).
>

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Otevřete PowerShell.
3. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.
4. Vyberte předplatné, které obsahuje datový sklad, který se má obnovit.
5. Získejte datový sklad, který chcete obnovit.
6. Vytvořte žádost o obnovení pro datový sklad.
7. Ověřte stav geograficky obnoveného datového skladu.
8. Pokud chcete po dokončení obnovení nakonfigurovat datový sklad, přečtěte si téma [Konfigurace databáze po obnovení]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
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

Pokud je zdrojová databáze povolena TDE, obnovená databáze bude TDE povolena.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Obnovení z geografické oblasti Azure prostřednictvím Azure Portal

Pomocí níže uvedeného postupu obnovte vyhrazený fond SQL (dřív SQL DW) z geografické zálohy:

1. Přihlaste se ke svému účtu [Azure Portal](https://portal.azure.com/) .
1. Vyhledejte **vyhrazené fondy SQL (dříve SQL DW)**.

   ![Nový DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

1. Klikněte na Přidat a vyplňte informace požadované na kartě **základy** a klikněte na **Další: Další nastavení**.

   ![Základy](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

1. Pro možnost **použít existující datový** parametr vyberte **zálohovat** a v možnostech posouvání vyberte příslušnou zálohu. Klikněte na **Zkontrolovat a vytvořit**.

   ![zálohování](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

1. Po obnovení datového skladu ověřte, zda je **stav** online.

## <a name="next-steps"></a>Další kroky

- [Obnovení existujícího vyhrazeného fondu SQL (dřív SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení odstraněného vyhrazeného fondu SQL (dřív SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
