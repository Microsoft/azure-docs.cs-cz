---
title: Převést Azure spravované disky úložiště ze standardní, Premium a naopak | Microsoft Docs
description: Postup převedení Azure spravovat disky z standardní, Premium a naopak a pomocí prostředí Azure PowerShell.
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
ms.openlocfilehash: ac3f1368fb6d3f31b75b581d56e07fe11c3722b3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Převést Azure spravované disky úložiště ze standardní, Premium a naopak

Spravované disky nabízí dvě možnosti úložiště: [Premium](premium-storage.md) (založené na jednotku SSD) a [standardní](standard-storage.md) (založené na HDD). Umožňuje snadno přepínat mezi dvě možnosti s minimálními výpadky podle vašim požadavkům na výkon. Tato funkce není k dispozici pro nespravovaná disky. Ale můžete snadno [převést na spravované disky](convert-unmanaged-to-managed-disks.md) snadno přepínat mezi dvě možnosti.

Tento článek ukazuje, jak převést spravované disky z standardní, Premium a naopak pomocí prostředí Azure PowerShell. Pokud je potřeba nainstalovat nebo upgradovat najdete v tématu [instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Než začnete

* Převod vyžaduje restartování virtuálního počítače, takže naplánovat migraci úložiště disků existující období údržby. 
* Pokud používáte nespravované disky, nejprve [převést na spravované disky](convert-unmanaged-to-managed-disks.md) používat v tomto článku přepínat mezi dvě možnosti úložiště. 
* Tento článek vyžaduje prostředí Azure PowerShell verze modulu 6.0.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Budete také muset spustit `Connect-AzureRmAccount` vytvořit připojení s Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Převeďte všechny spravované disky virtuálního počítače ze standardní premium a naopak

V následujícím příkladu ukážeme, jak přepínat všechny disky virtuálního počítače z standard do úložiště úrovně premium. Spravované prémiové disky, virtuální počítač vyžaduje použití [velikost virtuálního počítače](sizes.md) který podporuje službu premium storage. Tento příklad také přepne na velikost, která podporuje službu premium storage.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
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
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Převést standardní spravovaných disků na premium a naopak

Pro vývoj/testování úlohy můžete mít směs standard a premium disky na snížení nákladů na vaše. Můžete ji provést upgradem do úložiště úrovně premium, pouze disky, které vyžadují vyšší výkon. V následujícím příkladu ukážeme, jak přepínat jediný disk virtuálního počítače z standard do úložiště úrovně premium a naopak. Spravované prémiové disky, virtuální počítač vyžaduje použití [velikost virtuálního počítače](sizes.md) který podporuje službu premium storage. Tento příklad také přepne na velikost, která podporuje službu premium storage.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
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

## <a name="next-steps"></a>Další postup

Využít jen pro čtení kopie virtuálního počítače pomocí [snímky](snapshot-copy-managed-disk.md).

