---
title: Výměna disku s operačním systémem pro virtuální počítač Azure pomocí PowerShellu
description: Změna disku operačního systému používaného virtuálním počítačem Azure pomocí PowerShellu
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 8e928944a7508cc2a0ed35e89189fa2dd8c50665
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550378"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Změna disku s operačním systémem používaného virtuálním počítačem Azure pomocí PowerShellu

Pokud máte existující virtuální počítač, ale chcete disk zaměnit na záložní disk nebo jiný disk s operačním systémem, můžete k prohození disků s operačním systémem použít Azure PowerShell. Nemusíte odstranit a znovu vytvořit virtuální počítač. Můžete dokonce použít spravovaný disk v jiné skupině prostředků, pokud se ještě nepoužívá.

 

Virtuální počítač musí být stopped\deallocated, a ID prostředku spravovaného disku se dá nahradit ID prostředku jiného spravovaného disku.

Ujistěte se, že velikost virtuálního počítače a typ úložiště jsou kompatibilní s diskem, který chcete připojit. Pokud je například disk, který chcete použít, v Premium Storage, musí být virtuální počítač schopný Premium Storage (například velikost řady DS-Series). Oba disky musí mít také stejnou velikost.
A ujistěte se, že nepoužíváte nešifrovaný virtuální počítač s šifrovaným diskem s operačním systémem, to není podporováno. Pokud virtuální počítač nepoužívá Azure Disk Encryption, nepoužívá se Azure Disk Encryption disk s operačním systémem, který se používá.

Získání seznamu disků ve skupině prostředků pomocí [Get-AzDisk](/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Pokud máte název disku, který chcete použít, nastavte ho jako disk s operačním systémem pro virtuální počítač. V tomto příkladu se stop\deallocates virtuální počítač s názvem *myVM* a přiřadí se disk s názvem *newDisk* jako nový disk s operačním systémem. 
 
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

Pokud chcete vytvořit kopii disku, přečtěte si téma vytvoření [snímku disku](snapshot-copy-managed-disk.md).
