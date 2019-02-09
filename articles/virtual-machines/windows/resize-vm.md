---
title: Změnit velikost virtuálního počítače s Windows v Azure pomocí Powershellu | Dokumentace Microsoftu
description: Změna velikosti Windows virtuální počítač vytvořený v modelu nasazení Resource Manager pomocí Azure Powershellu.
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
ms.openlocfilehash: 0c942056e95812dfbbe6e3b1e8963799088273fb
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981192"
---
# <a name="resize-a-windows-vm"></a>Změna velikosti virtuálního počítače Windows

Tento článek ukazuje, jak přesunout virtuální počítač na jiný [velikost virtuálního počítače](sizes.md) pomocí Azure Powershellu.

Po vytvoření virtuálního počítače (VM), můžete škálovat virtuální počítač navýšit nebo snížit kapacitu tak, že změníte velikost virtuálního počítače. V některých případech je nejdřív uvolněte virtuální počítač. To může nastat, pokud nová velikost není k dispozici v clusteru hardwaru, který je aktuálně hostuje virtuální počítač.

Pokud váš virtuální počítač používá úložiště úrovně Premium, ujistěte se, abyste zvolili **s** verze velikosti pro získání podpory služby Premium Storage. Například zvolte Standard_E4**s**_v3 místo Standard_E4_v3.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Změna velikosti virtuálního počítače s Windows není ve skupině dostupnosti

Nastavte několik proměnných. Nahraďte hodnoty svými vlastními informacemi.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Seznam velikostí virtuálních počítačů, které jsou k dispozici na hardwaru clusteru, kde je virtuální počítač hostovaný. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Pokud je uvedený požadovanou velikost, spusťte následující příkazy ke změně velikosti virtuálního počítače. Pokud je požadovaná velikost není uveden, přejděte ke kroku 3.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Pokud není uvedené požadované velikosti, spusťte následující příkazy, které zrušit přidělení virtuálního počítače, změňte jeho velikost a restartujte virtuální počítač. Nahraďte **<newVMsize>** s požadovanou velikost.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Rušení přidělení virtuálního počítače uvolní všechny dynamické IP adresy přiřazené k virtuálnímu počítači. Operační systém a datové disky nejsou ovlivněny. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Změna velikosti virtuálního počítače s Windows ve skupině dostupnosti

Pokud novou velikost virtuálních počítačů ve skupině dostupnosti není k dispozici na hardwaru cluster aktuálně hostuje virtuální počítač, všechny virtuální počítače ve skupině dostupnosti bude nutné uvolnit pro změnu velikosti virtuálního počítače. Potřebujete také aktualizovat velikost ostatních virtuálních počítačů ve skupině dostupnosti po změně velikosti jeden virtuální počítač. Změna velikosti virtuálních počítačů ve skupině dostupnosti, postupujte následovně.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Seznam velikostí virtuálních počítačů, které jsou k dispozici na hardwaru clusteru, kde je virtuální počítač hostovaný. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Pokud je požadovaná velikost seznamu, spusťte následující příkazy ke změně velikosti virtuálního počítače. Pokud není uvedená, přejděte k další části.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Pokud není uvedené požadované velikosti, pokračujte následujícími kroky uvolnění všech virtuálních počítačů ve skupině dostupnosti, změna velikosti virtuálních počítačů a restartujte je.

Zastavte všechny virtuální počítače ve skupině dostupnosti.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Změna velikosti a restartování virtuálních počítačů ve skupině dostupnosti.
   
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

## <a name="next-steps"></a>Další postup

Pro dodatečnou škálovatelnost spusťte několik instancí virtuálních počítačů a horizontální navýšení kapacity. Další informace najdete v tématu [automatické škálování počítačů Windows ve Škálovací sadě virtuálních počítačů](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

