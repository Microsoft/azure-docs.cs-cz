---
title: Azure skript prostředí PowerShell ukázkový – vytvoření virtuálního počítače připojením se spravovaným diskem jako disk operačního systému | Microsoft Docs
description: Azure skript prostředí PowerShell ukázkový – vytvoření virtuálního počítače připojením se spravovaným diskem jako disk operačního systému
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
ms.openlocfilehash: ec532811e94647c8a04b9faf9474f6749969f83e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23928213"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Vytvoření virtuálního počítače pomocí existujícího spravovaného disku operačního systému pomocí prostředí PowerShell

Tento skript vytvoří virtuální počítač připojením existující spravované disk jako disk s operačním systémem. Pomocí tohoto skriptu v předchozích scénáře:
* Vytvoření virtuálního počítače z existujícího spravovaného disku operačního systému, který jste zkopírovali ze spravovaných disků v jiném předplatném.
* Vytvoření virtuálního počítače z existujícího spravovaného disku, který byl vytvořen z specializované soubor virtuálního pevného disku 
* Vytvoření virtuálního počítače z existujícího spravované disku operačního systému, který byl vytvořen ze snímku 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spusťte následující příkaz pro odebrání skupiny prostředků, virtuální počítač a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy získat vlastnosti spravovaných disků, připojte spravovaných disků na nový virtuální počítač a vytvoření virtuálního počítače. Každou položku v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmDisk](/powershell/module/azurerm.compute/Get-AzureRmDisk) | Získá objekt disku na základě názvu a skupině prostředků disku. Vlastnost ID objektu vrácený disku se používá k připojení disku k vytvoření nového virtuálního počítače |
| [Nové AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Vytvoří konfigurace virtuálního počítače. Tato konfigurace zahrnuje informace, jako je název virtuálního počítače, operační systém a pověření pro správu. Konfigurace se používá při vytváření virtuálních počítačů. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Připojí se spravovaným diskem pomocí vlastnosti Id disku jako disk operačního systému na nový virtuální počítač |
| [Nové AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Vytvoří veřejnou IP adresu. |
| [Nové AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Vytvoří rozhraní sítě. |
| [Nový AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Vytvoření virtuálního počítače. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odebere skupinu prostředků a všechny prostředky obsažené v rámci. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

Ukázky skriptu PowerShell další virtuální počítač nachází v [virtuálního počítače Windows Azure dokumentaci](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).