---
title: Ukázkový skript Azure PowerShellu – Služba IIS | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Služba IIS
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6f9322be5ed583435ef0e0396832139f7e77e318
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882460"
---
# <a name="create-an-iis-vm-with-powershell"></a>Vytvoření virtuálního počítače se službou IIS pomocí PowerShellu

Tento skript vytvoří virtuální počítač Azure s Windows Serverem 2016 a pak pomocí rozšíření vlastních skriptů virtuálního počítače Azure nainstaluje službu IIS. Po spuštění skriptu můžete k výchozímu webu služby IIS přistupovat na veřejné IP adrese virtuálního počítače.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Vytvoří virtuální počítač a připojí ho k síťové kartě, virtuální síti, podsíti a skupině zabezpečení sítě. Tento příkaz také otevře port 80 a nastaví přihlašovací údaje pro správu. |
| [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) | Přidá do virtuálního počítače rozšíření virtuálního počítače. V této ukázce se rozšíření vlastních skriptů používá k instalaci služby IIS. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro virtuální počítače najdete v [dokumentaci k virtuálním počítačům Azure s Windows](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
