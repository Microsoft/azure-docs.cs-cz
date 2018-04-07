---
title: Změna nastavení dostupnosti virtuální počítače | Microsoft Docs
description: Zjistěte, jak změnit sadu dostupnosti pro virtuální počítače pomocí Azure PowerShell a modelu nasazení Resource Manager.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 44c90f90-bc9a-4260-a36f-5465e2a1ef94
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: drewm
ms.openlocfilehash: 938614d7427f691dcacbafa324694a9a7f37d3ca
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Změňte sadu dostupnosti pro virtuální počítač s Windows
Následující kroky popisují postup změna nastavení dostupnosti virtuálního počítače pomocí Azure PowerShell. Virtuální počítač můžete přidat jenom při vytvoření sadu dostupnosti. Chcete-li změnit dostupnost nastaven, je potřeba odstranit a znovu vytvořte virtuální počítač. 

## <a name="change-the-availability-set-using-powershell"></a>Změnit skupinu dostupnosti pomocí prostředí PowerShell
1. Zaznamenejte následující podrobnosti klíče z virtuálního počítače, který má být změněn.
   
    Název virtuálního počítače
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <Name-of-resource-group> -Name <name-of-VM>
    $vm.Name
    ```
   
    Velikost virtuálního počítače
   
    ```powershell
    $vm.HardwareProfile.VmSize
    ```
   
    Primární síťové rozhraní sítě a volitelné síťových rozhraní, pokud existují ve virtuálním počítači
   
    ```powershell
    $vm.NetworkProfile.NetworkInterfaces[0].Id
    ```
   
    Disk s operačním systémem profilu
   
    ```powershell
    $vm.StorageProfile.OsDisk.OsType
    $vm.StorageProfile.OsDisk.Name
    $vm.StorageProfile.OsDisk.Vhd.Uri
    ```
   
    Profily disku pro každý datový disk 
   
    ```powershell
    $vm.StorageProfile.DataDisks[<index>].Lun
    $vm.StorageProfile.DataDisks[<index>].Vhd.Uri
    ```
   
    Nainstalovat rozšíření virtuálního počítače 
   
    ```powershell
    $vm.Extensions
    ```
2. Odstranění virtuálního počítače bez odstranění disky nebo síťová rozhraní.
   
    ```powershell
    Remove-AzureRmVM -ResourceGroupName <resourceGroupName> -Name <vmName> 
    ```
3. Vytvořit skupinu dostupnosti Pokud ještě neexistuje
   
    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName <resourceGroupName> -Name <availabilitySetName> -Location "<location>" 
    ```
4. Znovu vytvořte virtuální počítač pomocí nové sady dostupnosti.
   
    ```powershell
    $vm2 = New-AzureRmVMConfig -VMName <VM-name> -VMSize <vm-size> -AvailabilitySetId <availability-set-id>
   
    Set-AzureRmVMOSDisk -CreateOption "Attach" -VM <vmConfig> -VhdUri <osDiskURI> -Name <osDiskName> [-Windows | -Linux]
   
    Add-AzureRmVMNetworkInterface -VM <vmConfig> -Id  <nicId> 
   
    New-AzureRmVM -ResourceGroupName <resourceGroupName> -Location <location> -VM <vmConfig>
    ``` 
5. Přidejte datových disků a rozšíření. Další informace najdete v tématu [připojit datový Disk k virtuálnímu počítači](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a [rozšíření v šablonách Resource Manageru](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions). Datové disky a rozšíření lze přidat do virtuálního počítače pomocí prostředí PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="example-script"></a>Ukázkový skript
Následující skript představuje příklad shromažďování požadované informace, odstranit původní virtuální počítač a potom je znovu vytvořit v nové sady dostupnosti.

```powershell
    #set variables
    $rg = "demo-resource-group"
    $vmName = "demo-vm"
    $newAvailSetName = "demo-as"
    $outFile = "C:\temp\outfile.txt"

    #Get VM Details
    $OriginalVM = get-azurermvm -ResourceGroupName $rg -Name $vmName

    #Output VM details to file
    "VM Name: " | Out-File -FilePath $outFile 
    $OriginalVM.Name | Out-File -FilePath $outFile -Append

    "Extensions: " | Out-File -FilePath $outFile -Append
    $OriginalVM.Extensions | Out-File -FilePath $outFile -Append

    "VMSize: " | Out-File -FilePath $outFile -Append
    $OriginalVM.HardwareProfile.VmSize | Out-File -FilePath $outFile -Append

    "NIC: " | Out-File -FilePath $outFile -Append
    $OriginalVM.NetworkProfile.NetworkInterfaces[0].Id | Out-File -FilePath $outFile -Append

    "OSType: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.OsType | Out-File -FilePath $outFile -Append

    "OS Disk: " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.OsDisk.Vhd.Uri | Out-File -FilePath $outFile -Append

    if ($OriginalVM.StorageProfile.DataDisks) {
    "Data Disk(s): " | Out-File -FilePath $outFile -Append
    $OriginalVM.StorageProfile.DataDisks | Out-File -FilePath $outFile -Append
    }

    #Remove the original VM
    Remove-AzureRmVM -ResourceGroupName $rg -Name $vmName

    #Create new availability set if it does not exist
    $availSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -ErrorAction Ignore
    if (-Not $availSet) {
    $availset = New-AzureRmAvailabilitySet -ResourceGroupName $rg -Name $newAvailSetName -Location $OriginalVM.Location
    }

    #Create the basic configuration for the replacement VM
    $newVM = New-AzureRmVMConfig -VMName $OriginalVM.Name -VMSize $OriginalVM.HardwareProfile.VmSize -AvailabilitySetId $availSet.Id
    Set-AzureRmVMOSDisk -VM $NewVM -VhdUri $OriginalVM.StorageProfile.OsDisk.Vhd.Uri  -Name $OriginalVM.Name -CreateOption Attach -Windows

    #Add Data Disks
    foreach ($disk in $OriginalVM.StorageProfile.DataDisks ) { 
    Add-AzureRmVMDataDisk -VM $newVM -Name $disk.Name -VhdUri $disk.Vhd.Uri -Caching $disk.Caching -Lun $disk.Lun -CreateOption Attach -DiskSizeInGB $disk.DiskSizeGB
    }

    #Add NIC(s)
    foreach ($nic in $OriginalVM.NetworkProfile.NetworkInterfaces) {
        Add-AzureRmVMNetworkInterface -VM $NewVM -Id $nic.Id
    }

    #Create the VM
    New-AzureRmVM -ResourceGroupName $rg -Location $OriginalVM.Location -VM $NewVM -DisableBginfoExtension
```

## <a name="next-steps"></a>Další postup
Přidejte další úložiště k virtuálnímu počítači tak, že přidáte další [datový disk](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

