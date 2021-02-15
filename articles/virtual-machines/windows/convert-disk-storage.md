---
title: Převod úložiště spravovaných disků mezi různými typy disků pomocí Azure PowerShell
description: Jak převést spravované disky Azure mezi různými typy disků pomocí Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: 1d1c191c746d6853f922302d74c6eefcba547f80
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519741"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualizace typu úložiště spravovaného disku

Existují čtyři typy disků Azure Managed disks: disky Azure Ultra, Premium SSD, Standard SSD a standardní HDD. V závislosti na vašich požadavcích na výkon můžete přepínat mezi Premium SSD, Standard SSD a standardním pevným diskem. Ještě nejste schopni přepnout z nebo na disk Ultra, musíte nasadit nový.

Tato funkce není podporovaná pro nespravované disky. Nespravovaný disk ale můžete snadno [převést na spravovaný disk](convert-unmanaged-to-managed-disks.md) , aby bylo možné přepínat mezi typy disků.



## <a name="before-you-begin"></a>Než začnete

* Vzhledem k tomu, že převod vyžaduje restartování virtuálního počítače, měli byste naplánovat migraci diskového úložiště během již existujícího časového období údržby.
* Pokud je disk nespravovaný, nejprve [ho převeďte na spravovaný disk](convert-unmanaged-to-managed-disks.md) , abyste mohli přepínat mezi možnostmi úložiště.

## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Přepínání všech spravovaných disků virtuálního počítače z jednoho účtu na jiný

Tento příklad ukazuje, jak převést všechny disky virtuálního počítače na Premium Storage. Nicméně změnou proměnné $storageType v tomto příkladu můžete převést typ disků virtuálního počítače na standardní SSD nebo standardní pevný disk. Pokud chcete používat službu Premium Managed disks, musí mít virtuální počítač [Velikost virtuálního počítače](../sizes.md) , která podporuje Premium Storage. Tento příklad také přepíná na velikost, která podporuje Premium Storage:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS, StandardSDD_LRS and Premium_LRS based on your scenario
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

Pro vaše úlohy pro vývoj a testování můžete chtít snížit náklady na kombinaci standardních a prémiových disků. Můžete si vybrat, jestli chcete upgradovat jenom ty disky, které potřebují lepší výkon. Tento příklad ukazuje, jak převést jeden disk virtuálního počítače z úrovně Standard na Premium Storage. Nicméně změnou proměnné $storageType v tomto příkladu můžete převést typ disků virtuálního počítače na standardní SSD nebo standardní pevný disk. Pokud chcete používat službu Premium Managed disks, musí mít virtuální počítač [Velikost virtuálního počítače](../sizes.md) , která podporuje Premium Storage. Tento příklad také ukazuje, jak přepnout na velikost, která podporuje Premium Storage:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Přepnout spravované disky z jednoho typu na jiný disk

Postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. V seznamu **virtuálních počítačů** vyberte virtuální počítač.
3. Pokud se virtuální počítač nezastavil, v horní části podokna **přehledu** virtuálních počítačů vyberte **zastavit** a počkejte, než se virtuální počítač zastaví.
4. V podokně pro virtuální počítač vyberte z nabídky **disky** .
5. Vyberte disk, který chcete převést.
6. V nabídce vyberte **Konfigurace** .
7. Z původního typu disku změňte **typ účtu** na požadovaný typ disku.
8. Vyberte **Uložit** a zavřete podokno disk.

Převod typu disku je okamžitý. Po převodu můžete spustit virtuální počítač.

## <a name="next-steps"></a>Další kroky

Vytvořte kopii virtuálního počítače jen pro čtení pomocí [snímku](snapshot-copy-managed-disk.md).