---
title: Disk odkládacího souboru operačního systému pro virtuální počítač Azure pomocí prostředí PowerShell | Microsoft Docs
description: Změňte disk operačního systému používá virtuální počítač Azure pomocí prostředí PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: caa8fe2088995e3d30c9b808f639b9280e3a74be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32196193"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Změnit disk operačního systému používá virtuální počítač Azure pomocí prostředí PowerShell

Pokud máte existující virtuální počítač, ale chcete Prohodit disku pro zálohy disku nebo jiný disk operačního systému, můžete použít Azure PowerShell se Prohodit disky operačního systému. Nemáte odstranit a znovu vytvořte virtuální počítač. Spravovaných disků můžete použít i v jiné skupině prostředků, tak dlouho, dokud není již používán.

Virtuální počítač musí být stopped\deallocated a potom ID prostředku spravovaného disku lze nahradit s ID prostředku různých spravovaného disku.

Ujistěte se, že typ velikosti a úložiště virtuálního počítače jsou kompatibilní se disk, který chcete připojit. Například pokud je disk, který chcete použít v Storage úrovně Premium, pak virtuální počítač musí být schopný Storage úrovně Premium (jako je velikost řady DS). 

Získat seznam disků ve skupině prostředků pomocí [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk)

```azurepowershell-interactive
Get-AzureRmDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Až budete mít název na disk, který chcete použít, nastavte ho jako disk operačního systému pro virtuální počítač. Tento příklad stop\deallocates virtuálního počítače s názvem *Můjvp* a přiřadí disk s názvem *newDisk* jako nový disk operačního systému. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Další kroky**

Vytvoření kopie disku naleznete v tématu [snímku disk](snapshot-copy-managed-disk.md).