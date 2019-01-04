---
title: 'Skript prostředí PowerShell: Přírůstkové načtení dat pomocí služby Azure Data Factory | Dokumentace Microsoftu'
description: Tento skript prostředí PowerShell ukazuje, jak pomocí Azure Data Factory pro přírůstkové kopírování dat ze služby Azure SQL Database do úložiště objektů Blob v Azure...
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: a9866061396d3cd7548f7caa2ef0c7b32c1a1980
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015725"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>Skript prostředí PowerShell – přírůstkové načtení dat pomocí služby Azure Data Factory
Tento ukázkový skript Powershellu načte jenom nových nebo aktualizovaných záznamů ze zdrojového úložiště dat do úložiště dat jímky po počáteční úplné kopírování dat ze zdroje do jímky.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Zobrazit [kurz: přírůstkového kopírování](../tutorial-incremental-copy-powershell.md#prerequisites) předpoklady pro spuštěním této ukázky. 

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entity služby Data Factory (propojené služby, datové sady a kanál) na váš pevný disk do složky c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1 "Incremental copy from Azure SQL Database to Azure Blob Storage")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete k odebrání skupiny prostředků a všechny prostředky, které s ním spojená následující příkaz:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Chcete-li odebrat objekt pro vytváření dat ze skupiny prostředků, spusťte následující příkaz: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Vytvoření propojené služby ve službě data factory. Propojená služba propojuje úložiště dat nebo výpočetní výkon s datovou továrnou. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Vytvoří datovou sadu ve službě data factory. Datová sada představuje vstup/výstup pro aktivitu v kanálu. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2pipeline) | Ve službě data factory vytvoří kanál. Kanál obsahuje jednu nebo víc aktivit, které provádí určité operace. U tohoto kanálu aktivita kopírování kopíruje data z jednoho umístění do jiného umístění ve službě Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipeline) | Vytvoří spuštění kanálu. Jinými slovy spouští kanál. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Načte podrobnosti o spuštění aktivit (spuštění aktivit) v kanálu. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty Azure Powershellu objekt pro vytváření dat najdete v [skripty Powershellu pro Azure Data Factory](../samples-powershell.md).
