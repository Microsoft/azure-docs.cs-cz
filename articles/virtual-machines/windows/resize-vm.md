---
title: Změnit velikost virtuální počítač s Windows v Azure pomocí prostředí PowerShell | Microsoft Docs
description: Změňte velikost virtuálního počítače s Windows vytvořené v modelu nasazení Resource Manager pomocí Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 8854f694ce067aaa80a159430a9f48440bcdd95a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701866"
---
# <a name="resize-a-windows-vm"></a>Změnit velikost virtuálního počítače systému Windows

Tento článek ukazuje, jak přesunout virtuální počítač na jiný [velikost virtuálního počítače](sizes.md) pomocí Azure Powershell.

Po vytvoření virtuálního počítače (VM), je možné škálovat virtuální počítač nahoru nebo dolů změnou velikosti virtuálního počítače. V některých případech se musí nejprve navrácení virtuálního počítače. To může dojít, pokud nová velikost není k dispozici na hardwaru clusteru, který je aktuálně hostuje virtuální počítač.

Pokud vaše virtuální počítač používá úložiště úrovně Premium, ujistěte se, abyste zvolili **s** verzi velikost získat podporu Storage úrovně Premium. Například vyberte Standard_E4**s**_v3 místo Standard_E4_v3.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Změnit velikost virtuálního počítače Windows není v nastavení dostupnosti

Nastavte některé proměnné. Nahraďte hodnoty svými vlastními informacemi.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Zobrazí seznam velikosti virtuálních počítačů, které jsou k dispozici na hardwaru clusteru je hostitelem virtuálního počítače. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Pokud je uvedené požadované velikosti, spusťte následující příkazy ke změně velikosti virtuálního počítače. Pokud není uvedené požadované velikosti, přejděte ke kroku 3.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```

Pokud není uvedené požadované velikosti, spusťte následující příkazy se zrušit přidělení virtuálního počítače, jeho velikost a restartujte virtuální počítač. Replease **<newVMsize>** s požadovanou velikost.
   
```powershell
Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Rušení přidělení virtuálního počítače uvolní všechny dynamické IP adresy přiřazené k virtuálnímu počítači. Ovlivněné nejsou disky operačního systému a data. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Změnit velikost virtuální počítač s Windows v nastavení dostupnosti

Pokud velikost nového virtuálního počítače v nastavení dostupnosti není k dispozici na hardwaru cluster aktuálně hostuje virtuální počítač, všechny virtuální počítače v sadě dostupnosti bude muset být navrácena ke změně velikosti virtuálního počítače. Také může musíte aktualizovat velikost ostatních virtuálních počítačů ve skupině po změně velikosti jeden virtuální počítač dostupnosti. Ke změně velikosti virtuálních počítačů v nastavení dostupnosti, proveďte následující kroky.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Zobrazí seznam velikosti virtuálních počítačů, které jsou k dispozici na hardwaru clusteru je hostitelem virtuálního počítače. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Pokud požadovaná velikost je uveden, spusťte následující příkazy ke změně velikosti virtuálního počítače. Pokud ne, přejděte k další části.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Pokud není uvedené požadované velikosti, pokračujte následujícími kroky k navrácení všechny virtuální počítače v sadě dostupnosti, změně velikosti virtuálních počítačů a je restartovat.

Zastavte všechny virtuální počítače v sadě dostupnosti.
   
```powershell
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Změní velikost a restartovat virtuální počítače v sadě dostupnosti.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Další postup

Pro další škálovatelnost spustit více instancí virtuálního počítače a horizontální rozšíření kapacity. Další informace najdete v tématu [automaticky škálovat počítače s Windows v sadě škálování virtuálního počítače](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

