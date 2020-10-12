---
title: Obnovení datového skladu z geografické zálohy
description: Návod pro geografickou obnovu fondu SQL
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3c8d78c164cefbab03d9d3fa783c75ded9773d38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075808"
---
# <a name="geo-restore-for-sql-pool"></a>Geografické obnovení pro fond SQL

V tomto článku se naučíte, jak obnovit svůj fond SQL z geografické zálohy prostřednictvím Azure Portal a PowerShellu.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý fond SQL je hostovaný [logickým SQL serverem](../../azure-sql/database/logical-servers.md) (například MyServer.Database.Windows.NET), který má výchozí kvótu DTU. Ověřte, zda má systém SQL Server dostatek zbývajících kvót DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Obnovení z geografické oblasti Azure prostřednictvím PowerShellu

K obnovení z geografické zálohy použijte rutinu [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> K Gen2 můžete použít geografické obnovení. Provedete to tak, že jako volitelný parametr zadáte Gen2 ServiceObjectiveName (např. DW1000**c**).
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

Postupujte podle kroků uvedených níže a obnovte fond SQL z geografické zálohy:

1. Přihlaste se ke svému účtu [Azure Portal](https://portal.azure.com/) .
2. Klikněte na **+ Vytvořit prostředek**.

   ![Nový datový sklad](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Klikněte na **databáze** a potom na **Azure synapse Analytics (dřív SQL DW)**.

   ![Nový DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Vyplňte informace požadované na kartě **základy** a klikněte na **Další: Další nastavení**.

   ![Základy](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Pro možnost **použít existující datový** parametr vyberte **zálohovat** a v možnostech posouvání vyberte příslušnou zálohu. Klikněte na **Zkontrolovat a vytvořit**.

   ![zálohování](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Po obnovení datového skladu ověřte, zda je **stav** online.

## <a name="next-steps"></a>Další kroky

- [Obnovit existující fond SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení odstraněného fondu SQL](sql-data-warehouse-restore-deleted-dw.md)
