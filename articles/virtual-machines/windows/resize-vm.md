---
title: Změna velikosti virtuálního počítače s Windows v Azure
description: Změňte velikost virtuálního počítače používanou pro virtuální počítač Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2020
ms.author: cynthn
ms.openlocfilehash: 6718804d4635edb2628b53017ab9d377928afad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941725"
---
# <a name="resize-a-windows-vm"></a>Změna velikosti virtuálního počítače s Windows

Tento článek ukazuje, jak přesunout virtuální ho k jinému [virtuálnímu počítače](sizes.md).

Po vytvoření virtuálního počítače (VM) můžete škálovat virtuální počítač nahoru nebo dolů změnou velikosti virtuálního počítače. V některých případech je nutné nejprve navrátit virtuální ho. K tomu může dojít, pokud nová velikost není k dispozici v hardwarovém clusteru, který je aktuálně hostitelem virtuálního počítače.

Pokud váš virtuální počítač používá úložiště Premium Storage, ujistěte se, že jste zvolili verzi **s** velikosti, abyste získali podporu úložiště Premium. Místo Standard_E4_v3 například zvolte**Standard_E4**s _v3.

## <a name="use-the-portal"></a>Použití portálu

1. Otevřete [portál Azure](https://portal.azure.com).
1. Otevřete stránku virtuálního počítače.
1. V levé nabídce vyberte **Velikost**.
1. Vyberte novou velikost ze seznamu dostupných velikostí a pak vyberte **Změnit velikost**.


Pokud je virtuální počítač aktuálně spuštěn, změna jeho velikosti způsobí restartování. Zastavení virtuálního počítače může odhalit další velikosti.

## <a name="use-powershell-to-resize-a-vm-not-in-an-availability-set"></a>Změna velikosti virtuálního počítače, který není v sadě dostupnosti, použijte PowerShell

Nastavte některé proměnné. Nahraďte hodnoty vlastními informacemi.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Seznam velikostí virtuálních počítače, které jsou k dispozici v hardwarovém clusteru, kde je hostovaný virtuální počítač. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Pokud je uvedena požadovaná velikost, spusťte následující příkazy pro změna velikosti virtuálního počítače. Pokud požadovaná velikost není uvedena, přejděte ke kroku 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Pokud požadovaná velikost není uvedena, spusťte následující příkazy pro navrátit virtuální počítač, změnit jeho velikost a restartovat virtuální počítač. Nahraďte ** \<>newVMsize** požadovanou velikostí.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Zrušení přidělení virtuálního počítačů uvolní všechny dynamické IP adresy přiřazené k virtuálnímu jemu. Operační systém a datové disky nejsou ovlivněny. 
> 
> 

## <a name="use-powershell-to-resize-a-vm-in-an-availability-set"></a>Změna velikosti virtuálního počítače v sadě dostupnosti pomocí PowerShellu

Pokud nová velikost virtuálního počítače v sadě dostupnosti není k dispozici v hardwarovém clusteru, který aktuálně hostuje virtuální počítač, pak všechny virtuální počítače v sadě dostupnosti bude muset být přiděleny ke změně velikosti virtuálního počítače. Také může být nutné aktualizovat velikost jiných virtuálních počítače v sadě dostupnosti po změně velikosti jednoho virtuálního počítače. Chcete-li změnit velikost virtuálního počítače v sadě dostupnosti, proveďte následující kroky.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Seznam velikostí virtuálních počítače, které jsou k dispozici v hardwarovém clusteru, kde je hostovaný virtuální počítač. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Pokud je uvedena požadovaná velikost, spusťte následující příkazy pro změny velikosti virtuálního počítače. Pokud není uveden, přejděte k další části.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Pokud požadovaná velikost není uvedena, pokračujte následujícími kroky k navrátit všechny virtuální počítače v sadě dostupnosti, změnit velikost virtuálních počítačů a restartovat je.

Zastavte všechny virtuální ho disponibilní služby v sadě dostupnosti.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Změňte velikost a restartujte virtuální počítače v sadě dostupnosti.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Další kroky

Pro další škálovatelnost spusťte více instancí virtuálních počítačí a horizontální navýšení kapacity. Další informace naleznete v tématu [Automatické škálování počítačů s Windows v škálovací sadě virtuálních počítačů](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

