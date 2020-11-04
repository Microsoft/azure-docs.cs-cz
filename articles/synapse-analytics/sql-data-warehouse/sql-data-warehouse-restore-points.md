---
title: Uživatelem definované body obnovení
description: Postup vytvoření bodu obnovení pro vyhrazený fond SQL
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9d7266e0b84ae57682ddcfe7195be9574a702c74
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313249"
---
# <a name="user-defined-restore-points-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Uživatelem definované body obnovení pro vyhrazený fond SQL ve službě Azure synapse Analytics

V tomto článku se naučíte, jak vytvořit nový bod obnovení definovaný uživatelem pro vyhrazený fond SQL ve službě Azure synapse Analytics pomocí PowerShellu a Azure Portal.

## <a name="create-user-defined-restore-points-through-powershell"></a>Vytvoření uživatelem definovaných bodů obnovení prostřednictvím PowerShellu

Pokud chcete vytvořit bod obnovení definovaný uživatelem, použijte rutinu [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) prostředí PowerShell.

1. Než začnete, nezapomeňte [nainstalovat Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
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

2. Přejděte do vyhrazeného fondu SQL, pro který chcete vytvořit bod obnovení.

3. V levém podokně vyberte **Přehled** a vyberte **+ nový bod obnovení**. Pokud není vybráno tlačítko nový bod obnovení, ujistěte se, že vyhrazený fond SQL není pozastaven.

    ![Nový bod obnovení](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Zadejte název pro uživatelem definovaný bod obnovení a klikněte na **použít**. Uživatelem definované body obnovení mají výchozí dobu uchování sedm dní.

    ![Název bodu obnovení](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Další kroky

- [Obnovení existujícího vyhrazeného fondu SQL](sql-data-warehouse-restore-active-paused-dw.md)
- [Obnovení odstraněného vyhrazeného fondu SQL](sql-data-warehouse-restore-deleted-dw.md)
- [Obnovení z vyhrazeného fondu SQL geografického zálohování](sql-data-warehouse-restore-from-geo-backup.md)

