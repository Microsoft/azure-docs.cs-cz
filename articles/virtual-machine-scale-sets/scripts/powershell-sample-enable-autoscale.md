---
title: Ukázky Azure PowerShell – povolení automatického škálování na základě hostitele
description: Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016 a použije metriky hostitele k automatickému škálování s ohledem na měnící se zatížení procesoru.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 92411785419a1544b2d172d3a18661d501d4e5cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350843"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>Automatické škálování škálovací sady virtuálních počítačů pomocí PowerShellu
Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016 a použije metriky hostitele k automatickému škálování s ohledem na měnící se zatížení procesoru.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Ukázkový skript


[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Vytvoří škálovací sadu virtuálních počítačů a všechny podpůrné prostředky, včetně virtuální sítě, nástroje pro vyrovnávání zatížení a pravidel překladu adres. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Získá informace o škálovací sadě virtuálních počítačů. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Přidá do virtuálního počítače rozšíření vlastních skriptů pro instalaci základní webové aplikace. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Aktualizuje model škálovací sady virtuálních počítačů, aby se použilo rozšíření virtuálního počítače. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Získá informace o přiřazené veřejné IP adrese, kterou používá nástroj pro vyrovnávání zatížení. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky
Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro škálovací sady virtuálních počítačů najdete v [dokumentaci ke škálovacím sadám virtuálních počítačů Azure](../powershell-samples.md).
