---
title: Obnovení stávajícího datového skladu
description: Návod, jak obnovit existující Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a73658510111df44c522d88ed5eceb7dcfa80d0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685530"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Obnovit existující Azure SQL Data Warehouse

V tomto článku se naučíte obnovit existující SQL Data Warehouse prostřednictvím Azure Portal a PowerShellu:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ověřte svoji kapacitu DTU.** Každý SQL Data Warehouse hostuje SQL Server (například myserver.database.windows.net), který má výchozí kvótu DTU. Ověřte, zda má SQL Server dostatek zbývajících kvót DTU pro obnovenou databázi. Informace o tom, jak vypočítat potřebné DTU nebo požádat o více DTU, najdete v tématu [vyžádání změny kvóty DTU][Request a DTU quota change].

## <a name="before-you-begin"></a>Než začnete

1. Nezapomeňte [nainstalovat Azure PowerShell][Install Azure PowerShell].
2. Máte existující bod obnovení, ze kterého chcete obnovit. Pokud chcete vytvořit nové obnovení, přečtěte si [kurz vytvoření nového bodu obnovení definovaného uživatelem][the tutorial to create a new user-defined restore point].

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Obnovení existujícího datového skladu prostřednictvím PowerShellu

Chcete-li obnovit existující datový sklad z bodu obnovení, použijte rutinu [Restore-AzSqlDatabase][Restore-AzSqlDatabase] prostředí PowerShell.

1. Otevřete PowerShell.

2. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.

3. Vyberte předplatné, které obsahuje databázi, kterou chcete obnovit.

4. Seznam bodů obnovení datového skladu.

5. Vyberte požadovaný bod obnovení pomocí RestorePointCreationDate.

6. Obnovte datový sklad na požadovaný bod obnovení pomocí rutiny [Restore-AzSqlDatabase][Restore-AzSqlDatabase] prostředí PowerShell.
        1. Chcete-li obnovit SQL Data Warehouse na jiný logický Server, nezapomeňte zadat jiný název logického serveru.  Tento logický Server může být taky v jiné skupině prostředků a oblasti.
        2. K obnovení do jiného předplatného použijte tlačítko přesunout, aby se logický Server přesunul do jiného předplatného.

7. Ověřte, že je obnovený datový sklad online.

8. Po dokončení obnovení můžete po obnovení nakonfigurovat obnovený datový sklad pomocí [Konfigurace databáze][Configure your database after recovery].

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

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Obnovit existující datový sklad pomocí Azure Portal

1. Přihlaste se k webu [Azure Portal][Azure portal].
2. Přejděte na SQL Data Warehouse, ze kterého chcete obnovit.
3. V horní části okna Přehled vyberte **obnovit**.

    ![ Obnovení – přehled](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Vyberte buď **body automatického obnovení** nebo **body obnovení definované uživatelem**. Pokud datový sklad neobsahuje žádné automatické body obnovení, počkejte několik hodin nebo před obnovením vytvořte uživatelem definovaný bod obnovení. V případě uživatelem definovaných bodů obnovení vyberte existující nebo vytvořte novou. V případě **serveru**můžete vybrat logický Server v jiné skupině prostředků a oblasti nebo vytvořit nový. Po zadání všech parametrů klikněte na **zkontrolovat + obnovit**.

    ![Automatické body obnovení](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Další kroky
- [Obnovení odstraněného datového skladu][Restore a deleted data warehouse]
- [Obnovení z geograficky záložního datového skladu][Restore from a geo-backup data warehouse]
 
<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
