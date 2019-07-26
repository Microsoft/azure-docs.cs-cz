---
title: Obnovení Azure SQL Data Warehouse z geografické zálohy | Microsoft Docs
description: Návod pro geografickou obnovu Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 768646522c2589c302fd7a58031d4ebdb7fcdc12
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426652"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Geografické obnovení Azure SQL Data Warehouse

V tomto článku se naučíte obnovit datový sklad z geografické zálohy pomocí Azure Portal a PowerShellu.

## <a name="before-you-begin"></a>Před zahájením

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý SQL Data Warehouse hostuje SQL Server (například myserver.database.windows.net), který má výchozí kvótu DTU. Ověřte, zda má systém SQL Server dostatek zbývajících kvót DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU][Request a DTU quota change].

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Obnovení z geografické oblasti Azure prostřednictvím PowerShellu

K obnovení z geografické zálohy použijte rutinu [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] and [Restore-AzSqlDatabase][Restore-AzSqlDatabase] .

> [!NOTE]
> K Gen2 můžete použít geografické obnovení. Provedete to tak, že jako volitelný parametr zadáte Gen2 ServiceObjectiveName (např. DW1000**c**).
>

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell][Install Azure PowerShell].
2. Otevřete PowerShell.
2. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.
3. Vyberte předplatné, které obsahuje datový sklad, který se má obnovit.
4. Získejte datový sklad, který chcete obnovit.
5. Vytvořte žádost o obnovení pro datový sklad.
6. Ověřte stav geograficky obnoveného datového skladu.
7. Pokud chcete po dokončení obnovení nakonfigurovat datový sklad, přečtěte si téma [Konfigurace databáze po obnovení][Configure your database after recovery].

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

Pokud je zdrojová databáze povolena TDE, obnovená databáze bude TDE povolena.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Obnovení z geografické oblasti Azure prostřednictvím Azure Portal

Postupujte podle kroků uvedených níže a obnovte Azure SQL Data Warehouse z geografické zálohy:

1. Přihlaste se ke svému účtu [Azure Portal][Azure portal] .
1. Klikněte na **+ vytvořit prostředek** a vyhledejte SQL Data Warehouse a klikněte na **vytvořit**.

    ![Nový datový sklad](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Vyplňte informace požadované na kartě **základy** a klikněte na **další: Další nastavení**

    ![Základní informace](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. Pro možnost **použít existující datový** parametr vyberte **zálohovat** a v možnostech posouvání vyberte příslušnou zálohu. Klikněte na tlačítko **zkontrolovat + vytvořit**.
 
   ![zálohování](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Po obnovení datového skladu ověřte, zda je **stav** online.

## <a name="next-steps"></a>Další kroky
- [Obnovit existující datový sklad][Restore an existing data warehouse]
- [Obnovení odstraněného datového skladu][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
