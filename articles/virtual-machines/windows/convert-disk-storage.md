---
title: Převod úložiště spravovaných disků mezi standardním a prémiovým diskem SSD
description: Jak převést spravované disky Azure ze standardu na premium nebo premium na standardní pomocí Azure PowerShellu.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720941"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizace typu úložiště spravovaného disku

Existují čtyři typy disků spravovaných disků Azure: Azure ultra SSD (preview), premium SSD, standardní SSD a standardní HDD. Můžete přepínat mezi třemi typy disků GA (premium SSD, standardní SSD a standardní HDD) na základě vašich potřeb výkonu. Ještě nejste schopni přepnout z nebo na ultra SSD, musíte nasadit nový.

Tato funkce není podporována pro nespravované disky. Nespravovaný disk však můžete snadno [převést na spravovaný disk,](convert-unmanaged-to-managed-disks.md) abyste mohli přepínat mezi typy disků.

 

## <a name="prerequisites"></a>Požadavky

* Vzhledem k tomu, že převod vyžaduje restartování virtuálního počítače (VM), měli byste naplánovat migraci úložiště disku během již existujícího okna údržby.
* Pokud disk není spravovaný, [nejprve jej převeďte na spravovaný disk,](convert-unmanaged-to-managed-disks.md) abyste mohli přepínat mezi možnostmi úložiště.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Přepnutí všech spravovaných disků virtuálního počítače mezi premium a standard

Tento příklad ukazuje, jak převést všechny disky virtuálního počítače ze standardního úložiště na úložiště Premium nebo z úložiště Premium na standardní. Chcete-li používat disky spravované službou Premium, musí váš virtuální počítač používat [velikost virtuálního počítače,](sizes.md) která podporuje úložiště Premium. Tento příklad se také přepne na velikost, která podporuje úložiště premium:

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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Přepínání jednotlivých spravovaných disků mezi standardem a premium

Pro vaše úlohy pro vývoj a testování můžete chtít kombinaci disků Standard a Premium, abyste snížili náklady. Můžete provést upgrade pouze těch disků, které vyžadují lepší výkon. Tento příklad ukazuje, jak převést jeden disk virtuálního počítače ze standardního úložiště na úložiště Premium nebo z úložiště Premium na standardní. Chcete-li používat disky spravované službou Premium, musí váš virtuální počítač používat [velikost virtuálního počítače,](sizes.md) která podporuje úložiště Premium. Tento příklad také ukazuje, jak přepnout na velikost, která podporuje úložiště Premium:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Převod spravovaných disků ze standardu na Premium na webu Azure Portal

Postupujte následovně:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte virtuální počítač ze seznamu **virtuálních počítačů** na portálu.
3. Pokud virtuální hotel není zastaven, vyberte **Zastavit** v horní části podokna **Přehled** virtuálního zařízení a počkejte, až se virtuální hod zastaví.
3. V podokně virtuálního počítače vyberte **disky** z nabídky.
4. Vyberte disk, který chcete převést.
5. V nabídce vyberte **Konfigurace.**
6. Změňte **typ účtu** ze **standardního pevného disku** na Premium **SSD**.
7. Klepněte na tlačítko **Uložit**a zavřete podokno disku.

Převod typu disku je okamžitý. Virtuální počítač můžete spustit po převodu.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Přepínání spravovaných disků mezi standardním pevným diskem a standardním diskem SSD 

Tento příklad ukazuje, jak převést jeden disk virtuálního počítače ze standardního pevného disku na standardní ssd nebo ze standardního ssd disku na standardní pevný disk:

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Další kroky

Vytvořte kopii virtuálního počítače jen pro čtení pomocí [snímku](snapshot-copy-managed-disk.md).
