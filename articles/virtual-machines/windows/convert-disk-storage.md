---
title: Převod úložiště spravovaných disků mezi Standard a SSD
description: Jak převést službu Azure Managed disks z úrovně Standard na Premium nebo Premium na standard pomocí Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d81cd0ac57a5a18d90144584e8705cbffcba6f9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871424"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizace typu úložiště spravovaného disku

Existují čtyři typy disků Azure Managed disks: disky Azure Ultra, Premium SSD, Standard SSD a standardní HDD. Na základě vašich požadavků na výkon můžete přepínat mezi třemi typy disků GA (Premium SSD, Standard SSD a Standard HDD). Ještě nejste schopni přepnout z nebo na disk Ultra, musíte nasadit nový.

Tato funkce není podporovaná pro nespravované disky. Nespravovaný disk ale můžete snadno [převést na spravovaný disk](convert-unmanaged-to-managed-disks.md) , aby bylo možné přepínat mezi typy disků.

 

## <a name="prerequisites"></a>Požadavky

* Vzhledem k tomu, že převod vyžaduje restartování virtuálního počítače, měli byste naplánovat migraci diskového úložiště během již existujícího časového období údržby.
* Pokud je disk nespravovaný, nejprve [ho převeďte na spravovaný disk](convert-unmanaged-to-managed-disks.md) , abyste mohli přepínat mezi možnostmi úložiště.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Přepínání všech spravovaných disků virtuálního počítače v rámci úrovně Premium a Standard

Tento příklad ukazuje, jak převést všechny disky virtuálního počítače z úrovně Standard na Premium Storage nebo z úrovně Premium na úložiště Standard. Pokud chcete používat službu Premium Managed disks, musí mít virtuální počítač [Velikost virtuálního počítače](../sizes.md) , která podporuje Premium Storage. Tento příklad také přepíná na velikost, která podporuje Premium Storage:

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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Přepnout jednotlivé spravované disky mezi standardem a Premium

Pro vaše úlohy pro vývoj a testování můžete chtít snížit náklady na kombinaci standardních a prémiových disků. Můžete si vybrat, jestli chcete upgradovat jenom ty disky, které potřebují lepší výkon. Tento příklad ukazuje, jak převést jeden disk virtuálního počítače z úrovně Standard na Premium Storage nebo z úrovně Premium na úložiště úrovně Standard. Pokud chcete používat službu Premium Managed disks, musí mít virtuální počítač [Velikost virtuálního počítače](../sizes.md) , která podporuje Premium Storage. Tento příklad také ukazuje, jak přepnout na velikost, která podporuje Premium Storage:

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

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Převést spravované disky z úrovně Standard na Premium v Azure Portal

Postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Vyberte virtuální počítač ze seznamu **virtuálních počítačů** na portálu.
3. Pokud se virtuální počítač nezastavil, v horní části podokna **přehledu** virtuálních počítačů vyberte **zastavit** a počkejte, než se virtuální počítač zastaví.
3. V podokně pro virtuální počítač vyberte z nabídky **disky** .
4. Vyberte disk, který chcete převést.
5. V nabídce vyberte **Konfigurace** .
6. Změňte **typ účtu** z **HDD úrovně Standard** na **SSD úrovně Premium**.
7. Klikněte na **Uložit**a zavřete podokno disk.

Převod typu disku je okamžitý. Po převodu můžete spustit virtuální počítač.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Přepínat spravované disky mezi HDD úrovně Standard a SSD úrovně Standard 

Tento příklad ukazuje, jak převést jeden disk virtuálního počítače z HDD úrovně Standard na SSD úrovně Standard nebo z SSD úrovně Standard na HDD úrovně Standard:

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