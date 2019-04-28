---
title: Převést Azure spravované diskové úložiště úroveň ze Standard na Premium a Premium na Standard | Dokumentace Microsoftu
description: Jak převést Azure spravované disky úroveň ze Standard na Premium a Premium na Standard s využitím Azure Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd893c68f2e9cac713e09dd0bdafb7f277ae889
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766090"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizovat typ úložiště spravovaného disku

Existují čtyři možnosti pro Azure managed disks: Azure Ultra diskové úložiště, Premium SSD, SSD na úrovni Standard a Standard pevný disk. Můžete přepínat mezi těmito typy úložiště na základě vašich potřeb výkonu s malý výpadek. Tato funkce není podporována pro nespravované disky. Ale můžete snadno [převést nespravovaného disku na spravovaný disk](convert-unmanaged-to-managed-disks.md) mohli přepínat mezi typů disků nevidí.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

* Protože převod vyžaduje restartování virtuálního počítače (VM), měli byste naplánovat migraci diskové úložiště během už existujícího časového období údržby.
* Pokud je disk nespravované, nejprve [ho převést na spravovaný disk](convert-unmanaged-to-managed-disks.md) tak můžete přepínat mezi možnostmi úložiště.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Přepnout všechny spravované disky virtuálních počítačů mezi úrovní Premium a Standard

Tento příklad ukazuje, jak převést všechny disky Virtuálního počítače z úrovně Standard na Premium storage nebo z úrovně Premium na Standard storage. Pokud chcete použít spravované disky úrovně Premium, musíte použít váš virtuální počítač [velikost virtuálního počítače](sizes.md) , který podporuje storage úrovně Premium. Tento příklad také přepíná na hodnotu, která podporuje službu premium storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Přepnout jednotlivé spravované disky mezi úrovněmi Standard a Premium

Pro úlohy pro vývoj/testování můžete chtít kombinaci disků Standard a Premium pro snížení nákladů. Můžete upgradovat jenom disky, které je třeba vyšší výkon. Tento příklad ukazuje, jak převést jeden disk virtuálního počítače z úrovně Standard na Premium storage nebo z úrovně Premium na Standard storage. Pokud chcete použít spravované disky úrovně Premium, musíte použít váš virtuální počítač [velikost virtuálního počítače](sizes.md) , který podporuje storage úrovně Premium. Tento příklad také ukazuje, jak přepnout na hodnotu, která podporuje službu Premium storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Převést spravované disky Standard na Premium na webu Azure Portal

Postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte virtuální počítač ze seznamu **virtuálních počítačů** na portálu.
3. Pokud není virtuální počítač zastavený, vyberte **Zastavit** v horní části VM **přehled** podokno a vyčkat, než virtuální počítač zastavit.
3. V podokně pro virtuální počítač vyberte **disky** z nabídky.
4. Vyberte disk, který má být převeden.
5. Vyberte **konfigurace** z nabídky.
6. Změnit **typ účtu** z **standardní HDD** k **Premium SSD**.
7. Klikněte na tlačítko **Uložit**a zavřít podokno disku.

Převod typu disku se okamžité. Váš virtuální počítač můžete restartovat po převodu.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Přepnout mezi standardní disků HDD a SDD standardní spravované disky 

Tento příklad ukazuje, jak převést jeden disk virtuálního počítače ze standardního pevného disku na SSD na úrovni Standard nebo z SSD na úrovni Standard na standardní HDD:

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
