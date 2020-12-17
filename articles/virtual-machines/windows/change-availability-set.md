---
title: Změna skupiny dostupnosti virtuálních počítačů
description: Naučte se, jak změnit skupinu dostupnosti pro virtuální počítač pomocí Azure PowerShell.
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 8c0694bd1dc2fefed644dc91a0d649dd1a480428
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654536"
---
# <a name="change-the-availability-set-for-a-vm"></a>Změna skupiny dostupnosti pro virtuální počítač
Následující postup popisuje, jak změnit skupinu dostupnosti virtuálního počítače pomocí Azure PowerShell. Virtuální počítač se dá přidat do skupiny dostupnosti jenom při jeho vytvoření. Chcete-li změnit skupinu dostupnosti, je nutné odstranit a znovu vytvořit virtuální počítač. 

Tento článek se týká virtuálních počítačů se systémy Linux a Windows.

Tento článek byl naposledy testován na 2/12/2019 pomocí [Azure Cloud Shell](https://shell.azure.com/powershell) a [modulu AZ PowerShell](/powershell/azure/install-az-ps) Version 1.2.0.

V tomto příkladu se nekontroluje, jestli je virtuální počítač připojený k nástroji pro vyrovnávání zatížení. Pokud je váš virtuální počítač připojený k nástroji pro vyrovnávání zatížení, budete muset skript aktualizovat, aby tento případ zpracovával. 


## <a name="change-the-availability-set"></a>Změna skupiny dostupnosti 

Následující skript poskytuje příklad shromažďování požadovaných informací, odstranění původního virtuálního počítače a jeho opětovné vytvoření v nové skupině dostupnosti.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>Další kroky

Přidáním dalšího [datového disku](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)přidejte do virtuálního počítače další úložiště.
