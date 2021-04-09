---
title: Nasazení prostředí Azure SSIS Integration runtime pomocí PowerShellu
description: Tento skript PowerShellu vytvoří prostředí Azure-SSIS Integration runtime, které může spouštět balíčky SSIS v cloudu.
ms.service: data-factory
ms.topic: article
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 158e2db5261ce35713e1564cd24f0dd1d563ab78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370202"
---
# <a name="powershell-script---deploy-azure-ssis-integration-runtime"></a>PowerShellový skript – nasazení prostředí Azure-SSIS Integration runtime

Tento ukázkový skript PowerShellu vytvoří prostředí Azure-SSIS Integration runtime, které umožňuje spouštět balíčky SSIS v Azure.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/data-factory/deploy-azure-ssis-integration-runtime/deploy-azure-ssis-integration-runtime.ps1 "Deploy Azure-SSIS Integration Runtime")]

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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) | Vytvoří prostředí Azure-SSIS Integration runtime, které může spouštět balíčky SSIS v cloudu. |
| [Spustit – AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) | Spustí prostředí Azure-SSIS Integration runtime. |
| [Get-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/get-Azdatafactoryv2integrationruntime) | Načte informace o prostředí Azure-SSIS Integration runtime. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).

Další Azure Data Factory ukázkových skriptech PowerShellu najdete v [ukázkách Azure Data Factory PowerShellu](../samples-powershell.md).