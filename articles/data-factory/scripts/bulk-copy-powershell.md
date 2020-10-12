---
title: Hromadné kopírování dat pomocí PowerShellu
description: Tento skript PowerShellu ukazuje, jak použít Azure Data Factory ke kopírování dat ze zdrojového úložiště dat do cílového úložiště dat hromadně.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: ecfc9930c3387e19fd74d7cc52d11864ef25259a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89439023"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Skript PowerShellu – hromadné kopírování více tabulek pomocí Azure Data Factory

Tento ukázkový skript PowerShellu kopíruje data z několika tabulek v Azure SQL Database do služby Azure synapse Analytics (dřív SQL Data Warehouse).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Viz [kurz: hromadné kopírování](../tutorial-bulk-copy.md#prerequisites) požadavků pro spuštění této ukázky.

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují Data Factory entit (propojená služba, datová sada a kanál) na pevném disku v c:\. složky.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure Synapse Analytics")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Pokud chcete datovou továrnu ze skupiny prostředků odebrat, spusťte následující příkaz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Vytvoří propojenou službu v datové továrně. Propojená služba propojuje úložiště dat nebo výpočetní prostředky s datovou továrnou. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Vytvoří datovou sadu v datové továrně. Datová sada představuje vstup/výstup aktivity v kanálu. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Vytvoří v datové továrně kanál. Kanál obsahuje jednu nebo více aktivit, které provádějí určitou operaci. V tomto kanálu aktivita kopírování kopíruje data z jednoho umístění do jiného umístění v Azure Blob Storage. |
| [Invoke – AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Vytvoří běh pro kanál. Jinými slovy, spouští kanál. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Získá podrobnosti o spuštění aktivity (spuštění aktivity) v kanálu. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další Azure Data Factory ukázkových skriptech PowerShellu najdete v [Azure Data Factory skriptech PowerShellu](../samples-powershell.md).
