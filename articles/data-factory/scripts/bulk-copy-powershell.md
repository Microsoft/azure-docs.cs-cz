---
title: Hromadné kopírování dat pomocí PowerShellu
description: Tento skript Prostředí PowerShell ukazuje, jak pomocí Azure Data Factory kopírovat data ze zdrojového úložiště dat do cílového úložiště dat hromadně.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: efc79f82a2181099f832da0d4a17fc370bf4f7f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929872"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Skript PowerShellu – hromadné kopírování více tabulek pomocí Azure Data Factory

Tento ukázkový skript PowerShellu zkopíruje data z více tabulek v databázi Azure SQL do datového skladu Azure SQL.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Viz [kurz: hromadné kopírování](../tutorial-bulk-copy.md#prerequisites) pro předpoklady pro spuštění této ukázky.

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entity Data Factory (propojená služba, datová sada a kanál) na pevném disku v c:\ Složky.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure SQL Data Warehouse")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní spojené prostředky:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Chcete-li odebrat objekt pro vytváření dat ze skupiny prostředků, spusťte následující příkaz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Sada-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Vytvoří propojenou službu v datové továrně. Propojená služba propojuje úložiště dat nebo výpočetní prostředky s továrnou na data. |
| [Sada-AzDataFactoryV2Sada dat](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Vytvoří datovou sadu v datové továrně. Datová sada představuje vstup a výstup pro aktivitu v kanálu. | 
| [Kanál Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Vytvoří kanál v datové továrně. Kanál obsahuje jednu nebo více aktivit, které provádí určitou operaci. V tomto kanálu zkopíruje aktivitu kopírování data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Vytvoří spuštění pro kanál. Jinými slovy, spustí potrubí. |
| [Spustit get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Získá podrobnosti o spuštění aktivity (aktivita spustit) v kanálu. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Factory PowerShell unavují [skripty Azure Data Factory powershellu](../samples-powershell.md).
