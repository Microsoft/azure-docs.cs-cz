---
title: Převést Azure spravované disky úložiště úroveň ze standard na premium a naopak | Dokumentace Microsoftu
description: Jak převést Azure spravované disky ze standard na premium a naopak, pomocí Azure Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 676adb05178097c1802c0ff5b3a5da1bbb7b188c
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978370"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizovat typ úložiště spravovaného disku

Služba Azure Managed Disks nabízí tři možnosti Typ úložiště: [Premium SSD](../windows/premium-storage.md), [SSD na úrovni Standard](../windows/disks-standard-ssd.md), a [standardní HDD](../windows/standard-storage.md). Můžete přepínat mezi typy úložišť s minimálními výpadky, podle potřeb výkonu spravovaného disku. Přepínání mezi typy úložiště se nepodporuje pro nespravovaný disk; však můžete snadno [převést nespravovaného disku na spravovaný disk](convert-unmanaged-to-managed-disks.md).

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Požadavky

* Protože převod vyžaduje restartování virtuálního počítače (VM), měli byste naplánovat migraci disků úložiště během už existujícího časového období údržby. 
* Pokud používáte nespravovaný disk, nejprve [ho převést na spravovaný disk](convert-unmanaged-to-managed-disks.md) aby bylo možné přepínat mezi typy úložišť. 
* Příklady v tomto článku vyžadují modul Azure PowerShell verze 6.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-az-ps). Spustit [připojit AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) vytvořit připojení k Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>Převést všechny spravované disky virtuálního počítače z úrovně standard na premium

Následující příklad ukazuje, jak přepnout všechny disky virtuálního počítače z úrovně standard na premium storage. Chcete-li používat službu premium managed disks, musíte použít váš virtuální počítač [velikost virtuálního počítače](sizes.md) , který podporuje storage úrovně premium. Tento příklad také přepíná na hodnotu, která podporuje službu premium storage:

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
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Převést spravovaného disku ze standard na premium

Pro úlohy pro vývoj/testování můžete chtít kombinaci disků úrovně standard a premium ke snížení nákladů. Uděláte to tak, upgradujte na premium storage jenom disky, které vyžadují vyšší výkon. Následující příklad ukazuje, jak přepnout jeden disk virtuálního počítače ze standard na premium storage a naopak. Chcete-li používat službu premium managed disks, musíte použít váš virtuální počítač [velikost virtuálního počítače](sizes.md) , který podporuje storage úrovně premium. Tento příklad také ukazuje, jak přepnout na hodnotu, která podporuje službu premium storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Převést spravovaného disku ze standardní pevného disku na SSD na úrovni standard

Následující příklad ukazuje, jak přepnout jeden disk virtuálního počítače ze standardního pevný disk na SSD na úrovni standard a naopak:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Další postup

Vytvořit kopii jen pro čtení virtuálního počítače pomocí [snímku](snapshot-copy-managed-disk.md).

