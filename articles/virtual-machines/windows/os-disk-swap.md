---
title: Disk odkládacího souboru operačního systému pro virtuální počítač Azure pomocí Powershellu | Dokumentace Microsoftu
description: Změna disku operačního systému používaný virtuálním počítači Azure pomocí Powershellu.
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
ms.openlocfilehash: 73aab0750d97981d6684d04415683435bbd28797
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980410"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Změnit disk s operačním systémem používané Virtuálním počítači Azure pomocí Powershellu

Pokud máte existující virtuální počítač, ale chcete Prohodit disku pro zálohování disku nebo jiný disk s operačním systémem, můžete použít Azure PowerShell pro prohození disků operačního systému. Není nutné odstranit a znovu vytvořte virtuální počítač. Tak dlouho, dokud se ještě nepoužívá, můžete použít i spravovaného disku v jiné skupině prostředků.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Virtuální počítač musí být stopped\deallocated a ID prostředku spravovaného disku se dá nahradit výrazem ID prostředku různých spravovaného disku.

Ujistěte se, že typ velikosti a úložiště virtuálního počítače jsou kompatibilní s diskem, který chcete připojit. Například pokud ve službě Premium Storage se disk, který chcete použít, pak virtuální počítač musí být schopné Storage úrovně Premium (jako je velikost řady DS-series). 

Získání seznamu disků ve skupině prostředků pomocí [Get AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Až budete mít název, který chcete použít, nastavte ji jako disk s operačním systémem pro virtuální počítač. Tento příklad stop\deallocates virtuální počítač s názvem *myVM* a přiřadí disk s názvem *newDisk* jako nový disk s operačním systémem. 
 
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

Vytvoření kopie disku najdete v tématu [pořízení snímku disku](snapshot-copy-managed-disk.md).