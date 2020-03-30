---
title: Výměna disku operačního systému pro virtuální počítač Azure s Prostředím PowerShell
description: Změňte disk operačního systému používaný virtuálním počítačem Azure pomocí PowerShellu.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: ec66892804f3c2d1f831168a2955f2498462cbf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033026"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Změna disku operačního systému používaného virtuálním počítačem Azure pomocí PowerShellu

Pokud máte existující virtuální počítač, ale chcete vyměnit disk za záložní disk nebo jiný disk operačního systému, můžete použít Azure PowerShell k odhození disků operačního systému. Není nutné odstranit a znovu vytvořit virtuální ho. Spravovaný disk můžete dokonce použít v jiné skupině prostředků, pokud ještě není používán.

 

Virtuální modul je nutné zastavit\deallocated, pak ID prostředku spravovaného disku lze nahradit ID prostředku na jiném spravovaném disku.

Ujistěte se, že velikost virtuálního počítače a typ úložiště jsou kompatibilní s diskem, který chcete připojit. Například pokud disk, který chcete použít, je v úložišti Premium, pak virtuální modul musí být schopný úložiště Premium (například velikost řady DS). Oba disky musí mít také stejnou velikost.

Získání seznamu disků ve skupině prostředků pomocí [služby Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Pokud máte název disku, který chcete použít, nastavte jej jako disk operačního systému pro virtuální počítače. Tento příklad stop\naallocates virtuálního počítače s názvem *myVM* a přiřadí disk s názvem *newDisk* jako nový disk operačního systému. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Další kroky**

Chcete-li vytvořit kopii disku, přečtěte si [odkaz na snímek disku](snapshot-copy-managed-disk.md).
