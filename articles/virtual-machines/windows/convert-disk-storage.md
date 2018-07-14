---
title: Převést Azure spravované disky úložiště úroveň ze standard na premium a naopak | Dokumentace Microsoftu
description: Jak převést Azure spravované disky ze standard na premium a naopak, pomocí Azure Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 8ab7745c6b600ac20b6e6064108e15e7f8ab8b09
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991176"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Převést Azure spravované disky úložiště úroveň ze standard na premium a naopak

Managed Disks nabízí tři možnosti úložiště: [Premium SSD](../windows/premium-storage.md), standardní SSD(Preview) a [standardní HDD](../windows/standard-storage.md). Umožňuje snadno přepínat mezi možnostmi s minimálními výpadky na základě vašich potřeb výkonu. To není podporováno pro nespravované disky. Ale můžete snadno [převod na managed disks](convert-unmanaged-to-managed-disks.md) snadno přepínat mezi typy disků.

V tomto článku se dozvíte, jak převést spravované disky standard na premium a naopak pomocí Azure Powershellu. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Než začnete

* Převod vyžaduje restartování virtuálního počítače, proto naplánujte migraci disků úložiště během už existujícího časového období údržby. 
* Pokud používáte nespravované disky, nejprve [převod na managed disks](convert-unmanaged-to-managed-disks.md) přepínat mezi možnosti úložiště pomocí tohoto článku. 
* Tento článek vyžaduje modul Azure PowerShell verze 6.0.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Musíte také spustit příkaz `Connect-AzureRmAccount`, abyste vytvořili připojení k Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Převést všechny spravované disky virtuálního počítače ze standard na premium a naopak

Následující příklad ukazuje, jak přepnout všechny disky virtuálního počítače z úrovně standard na premium storage. Pokud chcete použít spravované disky úrovně premium, musíte použít váš virtuální počítač [velikost virtuálního počítače](sizes.md) , který podporuje storage úrovně premium. Tento příklad také přepíná na hodnotu, která podporuje službu premium storage.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Převést spravovaného disku ze standard na premium a naopak

Pro úlohy pro vývoj/testování můžete mít kombinaci disků úrovně standard a premium ke snížení nákladů. Můžete ji provést díky upgradu na premium storage, jenom disky, které vyžadují vyšší výkon. Následující příklad ukazuje, jak přepnout jeden disk virtuálního počítače ze standard na premium storage a naopak. Pokud chcete použít spravované disky úrovně premium, musíte použít váš virtuální počítač [velikost virtuálního počítače](sizes.md) , který podporuje storage úrovně premium. Tento příklad také přepíná na hodnotu, která podporuje službu premium storage.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.diskId

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Převést spravovaného disku ze standardní pevného disku na SSD na úrovni standard a naopak

Následující příklad ukazuje, jak přepnout jeden disk virtuálního počítače ze standardního pevný disk na SSD na úrovni standard a naopak.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.diskId

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Další postup

Pořiďte si jen pro čtení virtuálního počítače pomocí [snímky](snapshot-copy-managed-disk.md).

