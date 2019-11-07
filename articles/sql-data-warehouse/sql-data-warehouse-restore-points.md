---
title: Uživatelem definované body obnovení
description: Vytvoření bodu obnovení Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 61ceb2f1271d085321215aff1c6d138feb95d743
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692513"
---
# <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení

V tomto článku se naučíte, jak vytvořit nový bod obnovení definovaný uživatelem pro Azure SQL Data Warehouse pomocí PowerShellu a Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Vytvoření uživatelem definovaných bodů obnovení prostřednictvím PowerShellu

Pokud chcete vytvořit bod obnovení definovaný uživatelem, použijte rutinu [New-AzSqlDatabaseRestorePoint][New-AzSqlDatabaseRestorePoint] prostředí PowerShell.

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell][Install Azure PowerShell].
2. Otevřete PowerShell.
3. Připojte se k účtu Azure a seznamte se se všemi předplatnými přidruženými k vašemu účtu.
4. Vyberte předplatné, které obsahuje databázi, kterou chcete obnovit.
5. Vytvořte bod obnovení pro okamžitou kopii datového skladu.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Podívejte se na seznam všech existujících bodů obnovení.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Vytvoření bodů obnovení definovaných uživatelem pomocí Azure Portal

Uživatelem definované body obnovení lze také vytvořit prostřednictvím Azure Portal.

1. Přihlaste se ke svému účtu [Azure Portal][Azure portal] .

2. Přejděte do SQL Data Warehouse, pro který chcete vytvořit bod obnovení.

3. V levém podokně vyberte **Přehled** a vyberte **+ nový bod obnovení**. Pokud není vybráno tlačítko nový bod obnovení, ujistěte se, že datový sklad není pozastaven.

    ![Nový bod obnovení](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Zadejte název pro uživatelem definovaný bod obnovení a klikněte na **použít**. Uživatelem definované body obnovení mají výchozí dobu uchování sedm dní.

    ![Název bodu obnovení](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Další kroky

- [Obnovit existující datový sklad][Restore an existing data warehouse]
- [Obnovení odstraněného datového skladu][Restore a deleted data warehouse]
- [Obnovení z geograficky záložního datového skladu][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
