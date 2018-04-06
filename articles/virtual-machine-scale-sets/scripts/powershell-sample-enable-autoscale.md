---
title: Ukázky Azure PowerShellu – Povolení automatického škálování na základě hostitele | Microsoft Docs
description: Ukázky Azure PowerShellu
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: efa608cb1efa3fed48113adea45f15a2cf075439
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>Automatické škálování škálovací sady virtuálních počítačů pomocí PowerShellu
Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016 a použije metriky hostitele k automatickému škálování s ohledem na měnící se zatížení procesoru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Vytvoří škálovací sadu virtuálních počítačů a všechny podpůrné prostředky, včetně virtuální sítě, nástroje pro vyrovnávání zatížení a pravidel překladu adres. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Získá informace o škálovací sadě virtuálních počítačů. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Přidá do virtuálního počítače rozšíření vlastních skriptů pro instalaci základní webové aplikace. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Aktualizuje model škálovací sady virtuálních počítačů, aby se použilo rozšíření virtuálního počítače. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Získá informace o přiřazené veřejné IP adrese, kterou používá nástroj pro vyrovnávání zatížení. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky
Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro škálovací sady virtuálních počítačů najdete v [dokumentaci ke škálovacím sadám virtuálních počítačů Azure](../powershell-samples.md).