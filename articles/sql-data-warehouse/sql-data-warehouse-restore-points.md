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
ms.openlocfilehash: bf092b5b6c6eb88b565a940de56d614426e34d8e
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759597"
---
# <a name="user-defined-restore-points"></a>Uživatelem definované body obnovení

V tomto článku se naučíte, jak vytvořit nový bod obnovení definovaný uživatelem pro Azure SQL Data Warehouse pomocí PowerShellu a Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Vytvoření uživatelem definovaných bodů obnovení prostřednictvím PowerShellu

Pokud chcete vytvořit bod obnovení definovaný uživatelem, použijte rutinu [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) prostředí PowerShell.

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
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

1. Přihlaste se ke svému účtu [Azure Portal](https://portal.azure.com/) .

2. Přejděte do SQL Data Warehouse, pro který chcete vytvořit bod obnovení.

3. V levém podokně vyberte **Přehled** a vyberte **+ nový bod obnovení**. Pokud není vybráno tlačítko nový bod obnovení, ujistěte se, že datový sklad není pozastaven.

    ![Nový bod obnovení](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Zadejte název pro uživatelem definovaný bod obnovení a klikněte na **použít**. Uživatelem definované body obnovení mají výchozí dobu uchování sedm dní.

    ![Název bodu obnovení](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Další kroky

- [Obnovit existující datový sklad](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení odstraněného datového skladu](sql-data-warehouse-restore-deleted-dw.md)
- [Obnovení z geograficky záložního datového skladu](sql-data-warehouse-restore-from-geo-backup.md)

