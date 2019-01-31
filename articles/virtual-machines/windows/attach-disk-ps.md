---
title: Připojení datového disku k virtuálnímu počítači s Windows v Azure pomocí Powershellu | Dokumentace Microsoftu
description: Jak připojit nový nebo existující datový disk k virtuálnímu počítači s Windows pomocí modelu nasazení Resource Manageru pomocí Powershellu.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: abfaf76743f42a3fc4a13878d528c755feeef42a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458563"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu

Tento článek ukazuje, jak nové i stávající disky připojit k virtuálnímu počítači Windows pomocí prostředí PowerShell. 

Poprvé přečtěte si tyto typy:
* Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Další informace najdete v tématu [velikosti virtuálních počítačů](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Pokud chcete používat Premium storage, bude nutné typu virtuálního počítače podporující Premium Storage jako řady DS nebo GS-series virtuálních počítačů. Další informace najdete v tématu [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Chcete-li nainstalovat a používat PowerShell místně, v tomto kurzu potřebovat modul Azure PowerShell verze 6.0.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, bude také potřeba spustit `Connect-AzureRmAccount` vytvořit připojení k Azure.


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Přidat prázdný datový disk k virtuálnímu počítači

Tento příklad ukazuje, jak přidat prázdný datový disk do existujícího virtuálního počítače.

### <a name="using-managed-disks"></a>Použití spravovaných disků

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Použití spravovaných disků v zóně dostupnosti
Chcete-li vytvořit disk v zóně dostupnosti, použijte [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig) s `-Zone` parametru. Následující příklad vytvoří disk v zóně *1*.


```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```


### <a name="initialize-the-disk"></a>Inicializujte disk

Po přidání prázdný disk bude potřeba inicializovat. Chcete-li inicializovat disk, můžete přihlásit k virtuálnímu počítači a použijte program Správa disků. Pokud jste povolili [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) a certifikát na virtuálním počítači při jeho vytváření, můžete použít vzdálený PowerShell inicializovat disk. Můžete také použít rozšíření vlastních skriptů: 

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
Soubor skriptu může obsahovat kód pro inicializaci disky, například:

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


## <a name="attach-an-existing-data-disk-to-a-vm"></a>Připojit stávající datový disk k virtuálnímu počítači

Existujícího spravovaného disku k virtuálnímu počítači můžete připojit jako datový disk. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Další postup

Vytvoření [snímku](snapshot-copy-managed-disk.md).
