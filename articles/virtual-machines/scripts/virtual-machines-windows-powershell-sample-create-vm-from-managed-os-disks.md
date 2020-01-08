---
title: Vytvoření virtuálního počítače připojením spravovaného disku jako disku s operačním systémem – ukázka PowerShellu
description: Ukázkový skript Azure PowerShellu – Vytvoření virtuálního počítače připojením spravovaného disku jako disku s operačním systémem
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 4514940918f33ee28fc4195b821018df54252e13
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75368484"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Vytvoření virtuálního počítače s použitím existujícího spravovaného disku s operačním systémem pomocí PowerShellu

Tento skript vytvoří virtuální počítač připojením existujícího spravovaného disku jako disku s operačním systémem. Tento skript můžete použít v následujících scénářích:
* Vytvoření virtuálního počítače z existujícího spravovaného disku s operačním systémem zkopírovaného ze spravovaného disku v jiném předplatném
* Vytvoření virtuálního počítače z existujícího spravovaného disku vytvořeného ze specializovaného souboru VHD 
* Vytvoření virtuálního počítače z existujícího spravovaného disku s operačním systémem vytvořeného ze snímku 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k získání vlastností spravovaného disku, připojení spravovaného disku k novému virtuálnímu počítači a vytvoření virtuálního počítače používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/Get-AzDisk) | Získá objekt disku na základě názvu a skupiny prostředků disku. Vlastnost ID vráceného objektu disku se použije k připojení disku k novému virtuálnímu počítači. |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Vytvoří konfiguraci virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a přihlašovací údaje pro správu. Tato konfigurace se použije při vytváření virtuálního počítače. |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | S použitím vlastnosti ID disku připojí spravovaný disk jako disk s operačním systémem k novému virtuálnímu počítači. |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Vytvoří síťové rozhraní. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Vytvoří virtuální počítač. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

V případě imagí z Marketplace nastavte informace o plánu pomocí [set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) .

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
