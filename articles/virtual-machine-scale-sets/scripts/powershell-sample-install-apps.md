---
title: Ukázky Azure PowerShell – instalace aplikací
description: Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016 a pomocí rozšíření vlastních skriptů nainstaluje základní webovou aplikaci.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 0f985d9f43074f8dc9f8ad93aba71e2dce8ae7b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932263"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-powershell"></a>Instalace aplikací do škálovací sady virtuálních počítačů pomocí Azure PowerShellu
Tento skript vytvoří škálovací sadu virtuálních počítačů s Windows Serverem 2016 a pomocí rozšíření vlastních skriptů nainstaluje základní webovou aplikaci. Po spuštění skriptu můžete k webové aplikaci přistupovat přes webový prohlížeč.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/install-apps/install-apps.ps1 "Install apps into a scale set")]

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
| [Update – AzVmss](/powershell/module/az.compute/update-azvmss) | Aktualizuje model škálovací sady virtuálních počítačů, aby se použilo rozšíření virtuálního počítače. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Získá informace o přiřazené veřejné IP adrese, kterou používá nástroj pro vyrovnávání zatížení. |
|  [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky
Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).
