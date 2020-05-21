---
title: Ukázky Azure PowerShell – použití vlastní image virtuálního počítače
description: Tento skript vytvoří škálovací sadu virtuálních počítačů, která jako zdroj pro instance virtuálních počítačů používá vlastní image virtuálního počítače.
author: axayjo
ms.author: akjosh
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 03/27/2018
ms.reviewer: cynthn
ms.custom: akjosh
ms.openlocfilehash: bdd36a2c58bddce5bea0ba43e5d21da9ef89a14d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699473"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-powershell"></a>Vytvoření škálovací sady virtuálních počítačů z vlastní image virtuálního počítače pomocí PowerShellu
Tento skript vytvoří škálovací sadu virtuálních počítačů, která jako zdroj pro instance virtuálních počítačů používá vlastní image virtuálního počítače.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.ps1 "Create a virtual machine scale set with a custom VM image")]

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky
Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).
