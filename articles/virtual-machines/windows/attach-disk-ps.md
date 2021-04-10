---
title: Připojení datového disku k virtuálnímu počítači s Windows v Azure pomocí PowerShellu
description: Jak připojit nový nebo existující datový disk k virtuálnímu počítači s Windows pomocí PowerShellu s modelem nasazení Správce prostředků.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d5c638a63e4e8dc1a55c07f4bdf713fd36ca6b3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550871"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Připojení datového disku k virtuálnímu počítači s Windows pomocí PowerShellu

V tomto článku se dozvíte, jak připojit nové i stávající disky k virtuálnímu počítači s Windows pomocí PowerShellu. 

Nejprve si přečtěte tyto tipy:

* Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Další informace najdete v tématu [velikosti pro virtuální počítače](../sizes.md).
* Pokud chcete používat prémiové SSD, budete potřebovat [typ virtuálního počítače s podporou úložiště Premium](../sizes-memory.md), jako je třeba virtuální počítač řady DS-Series nebo GS-Series.

Tento článek používá PowerShell v rámci [Azure Cloud Shell](../../cloud-shell/overview.md), který se průběžně aktualizuje na nejnovější verzi. Chcete-li otevřít Cloud Shell, vyberte možnost **vyzkoušet** v horní části libovolného bloku kódu.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Přidání prázdného datového disku k virtuálnímu počítači

Tento příklad ukazuje, jak přidat prázdný datový disk k existujícímu virtuálnímu počítači.

### <a name="using-managed-disks"></a>Použití spravovaných disků

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Použití spravovaných disků v zóně dostupnosti

Pokud chcete vytvořit disk v zóně dostupnosti, použijte [příkaz New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) s `-Zone` parametrem. Následující příklad vytvoří disk v zóně *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Inicializovat disk

Po přidání prázdného disku ho budete muset inicializovat. Pokud chcete disk inicializovat, můžete se přihlásit k virtuálnímu počítači a používat správu disků. Pokud jste povolili službu [WinRM](/windows/desktop/winrm/portal) a certifikát na virtuálním počítači, když jste ji vytvořili, můžete k inicializaci disku použít vzdálenou PowerShell. Můžete také použít rozšíření vlastních skriptů:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Soubor skriptu může obsahovat kód pro inicializaci disků, například:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Připojit existující datový disk k virtuálnímu počítači

Existující spravovaný disk můžete připojit k virtuálnímu počítači jako datový disk.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Další kroky

Spravované disky můžete nasadit také pomocí šablon. Další informace najdete v tématu [použití Managed disks v šablonách Azure Resource Manager](../using-managed-disks-template-deployments.md) nebo v [šabloně pro rychlé](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) zprovoznění pro nasazení více datových disků.