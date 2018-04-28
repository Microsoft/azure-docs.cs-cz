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
ms.date: 10/19/2016
ms.author: cynthn
ms.openlocfilehash: d2010ee9017416360069c74118b8ae25e71e1da7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="resize-a-windows-vm"></a>Změnit velikost virtuálního počítače systému Windows
Tento článek ukazuje, jak změnit velikost virtuálního počítače Windows, vytvořené v modelu nasazení Resource Manager pomocí Azure Powershell.

Po vytvoření virtuálního počítače (VM), je možné škálovat virtuální počítač nahoru nebo dolů změnou velikosti virtuálního počítače. V některých případech se musí nejprve navrácení virtuálního počítače. To může dojít, pokud nová velikost není k dispozici na hardwaru clusteru, který je aktuálně hostuje virtuální počítač.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Změnit velikost virtuálního počítače Windows není v nastavení dostupnosti
1. Zobrazí seznam velikosti virtuálních počítačů, které jsou k dispozici na hardwaru clusteru je hostitelem virtuálního počítače. 
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```
2. Pokud požadovaná velikost je uveden, spusťte následující příkazy ke změně velikosti virtuálního počítače. Pokud není uvedené požadované velikosti, přejděte ke kroku 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Pokud není uvedené požadované velikosti, spusťte následující příkazy se zrušit přidělení virtuálního počítače, jeho velikost a restartujte virtuální počítač.
   
    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -Name $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [!WARNING]
> Rušení přidělení virtuálního počítače uvolní všechny dynamické IP adresy přiřazené k virtuálnímu počítači. Ovlivněné nejsou disky operačního systému a data. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Změnit velikost virtuální počítač s Windows v nastavení dostupnosti
Pokud velikost nového virtuálního počítače v nastavení dostupnosti není k dispozici na hardwaru cluster aktuálně hostuje virtuální počítač, všechny virtuální počítače v sadě dostupnosti bude muset být navrácena ke změně velikosti virtuálního počítače. Také může musíte aktualizovat velikost ostatních virtuálních počítačů ve skupině po změně velikosti jeden virtuální počítač dostupnosti. Ke změně velikosti virtuálních počítačů v nastavení dostupnosti, proveďte následující kroky.

1. Zobrazí seznam velikosti virtuálních počítačů, které jsou k dispozici na hardwaru clusteru je hostitelem virtuálního počítače.
   
    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```
2. Pokud požadovaná velikost je uveden, spusťte následující příkazy ke změně velikosti virtuálního počítače. Pokud není v seznamu uvedena, přejděte ke kroku 3.
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```
3. Pokud není uvedené požadované velikosti, pokračujte následujícími kroky k navrácení všechny virtuální počítače v sadě dostupnosti, změně velikosti virtuálních počítačů a je restartovat.
4. Zastavte všechny virtuální počítače v sadě dostupnosti.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
   } 
   ```
5. Změní velikost a restartovat virtuální počítače v sadě dostupnosti.
   
   ```powershell
   $rg = "<resourceGroupName>"
   $newSize = "<newVmSize>"
   $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
   $vmIds = $as.VirtualMachinesReferences
   foreach ($vmId in $vmIDs){
     $string = $vmID.Id.Split("/")
     $vmName = $string[8]
     $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
     $vm.HardwareProfile.VmSize = $newSize
     Update-AzureRmVM -ResourceGroupName $rg -VM $vm
     Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
   }
   ```

## <a name="next-steps"></a>Další postup
* Pro další škálovatelnost spustit více instancí virtuálního počítače a horizontální rozšíření kapacity. Další informace najdete v tématu [automaticky škálovat počítače s Windows v sadě škálování virtuálního počítače](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

