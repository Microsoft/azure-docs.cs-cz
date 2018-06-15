---
title: Azure skript prostředí PowerShell ukázkový – vytvoření virtuálního počítače ze snímku | Microsoft Docs
description: Azure skript prostředí PowerShell ukázkový – vytvoření virtuálního počítače ze snímku
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 63d108bbfd0f58f8a40bf1c7c8649e3a1f7ed288
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23879775"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell"></a>Vytvoření virtuálního počítače ze snímku pomocí prostředí PowerShell

Tento skript vytvoří virtuální počítač ze snímku disk s operačním systémem. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spusťte následující příkaz pro odebrání skupiny prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy a získat vlastnosti snímku, vytvoření spravovaného disku ze snímku vytvoření virtuálního počítače. Každou položku v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/get-azurermsnapshot) | Získá snímek pomocí názvu snímku. |
| [Nové AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) | Vytvoří konfiguraci disku. Tato konfigurace se používá s procesem vytvoření disku. |
| [Nové AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) | Vytvoří se spravovaným diskem. |
| [Nové AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Vytvoří konfigurace virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a pověření pro správu. Konfigurace se používá při vytváření virtuálních počítačů. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Připojí spravované disk jako disk operačního systému k virtuálnímu počítači |
| [Nové AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [Nové AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Vytvoří rozhraní sítě. |
| [Nový AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Vytvoří virtuální počítač. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky obsažené v rámci. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

Ukázky skriptu PowerShell další virtuální počítač nachází v [virtuálního počítače Windows Azure dokumentaci](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).