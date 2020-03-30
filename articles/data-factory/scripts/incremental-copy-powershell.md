---
title: Přírůstkové načítání dat pomocí PowerShellu
description: Tento skript Prostředí PowerShell ukazuje, jak pomocí Azure Data Factory postupně kopírovat data z azure sql databáze do úložiště objektů blob Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 1919f89d2e39981effe14c1203446f8f9d930f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79462485"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>Skript PowerShellu – přírůstkové načítání dat pomocí Azure Data Factory

Tento ukázkový skript prostředí PowerShell načte pouze nové nebo aktualizované záznamy ze zdrojového úložiště dat do úložiště dat jímky po počáteční úplné kopii dat ze zdroje do jímky.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Viz [kurz: přírůstková kopie](../tutorial-incremental-copy-powershell.md#prerequisites) pro předpoklady pro spuštění této ukázky. 

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entity Data Factory (propojená služba, datová sada a kanál) na pevném disku v c:\ Složky.

:::code language="powershell" source="~/powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1":::

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
| [Sada-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Vytvoří propojenou službu v datové továrně. Propojená služba propojuje úložiště dat nebo výpočetní prostředky s továrnou na data. |
| [Sada-AzDataFactoryV2Sada dat](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Vytvoří datovou sadu v datové továrně. Datová sada představuje vstup a výstup pro aktivitu v kanálu. | 
| [Kanál Set-AzDataFactoryV2](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Vytvoří kanál v datové továrně. Kanál obsahuje jednu nebo více aktivit, které provádějí určitou operaci. V tomto kanálu zkopíruje aktivitu kopírování data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Vytvoří spuštění pro kanál. Jinými slovy, spustí potrubí. |
| [Spustit get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Získá podrobnosti o spuštění aktivity (aktivita spustit) v kanálu. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Factory PowerShell unavují [skripty Azure Data Factory powershellu](../samples-powershell.md).
