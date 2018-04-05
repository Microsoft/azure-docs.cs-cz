---
title: Vyhledejte a odstraňte odpojit spravovanými a nespravovanými disky Azure | Microsoft Docs
description: Postup nalezení a odstranění odpojit spravovaných a nespravovaných (objekty BLOB VHD/stránky) disky systému Azure pomocí Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ramankum
ms.openlocfilehash: d908cdcd9e77f91a726f985d21bdc5bbc80ffd27
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Vyhledejte a odstraňte odpojit spravovanými a nespravovanými disky systému Azure
Při odstranění virtuálního počítače (VM) v Azure, ve výchozím nastavení, nebudou odstraněny všechny disky, které jsou připojené k virtuálnímu počítači. Tato funkce pomáhá zabránit ztrátě dat v důsledku neúmyslné odstranění virtuálních počítačů. Po odstranění virtuálního počítače, bude platit pro disky odpojit. Tento článek ukazuje, jak najít a odstraňte požadované disky odpojit a omezení zbytečných nákladů. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Spravované disky: Vyhledejte a odstraňte odpojit disky 

Následující skript hledá odpojit [discích spravovaných](managed-disks-overview.md) tak, že prověří hodnotu **spravované** vlastnost. Když se spravovaným diskem je připojen k virtuálnímu počítači, **spravované** vlastnost obsahuje ID prostředku virtuálního počítače. Po odpojit, spravovaných disků **spravované** vlastnost má hodnotu null. Skript kontroluje všechny disky, které jsou spravované v předplatné Azure. Pokud skript vyhledá spravované disk s **spravované** vlastnost nastavena na hodnotu null, skript zjistí, že je disk odpojit.

>[!IMPORTANT]
>Nejprve spusťte skript nastavením **deleteUnattachedDisks** proměnné na hodnotu 0. Tato akce umožňuje vyhledat a zobrazit všechny nepřipojené spravované disky.
>
>Po prostudování všech odpojit disků, znovu spusťte skript a nastavte **deleteUnattachedDisks** proměnné na hodnotu 1. Tato akce umožňuje odstranit všechny nepřipojené spravované disky.
>

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: Vyhledejte a odstraňte odpojit disky 

Nespravované disky jsou soubory virtuálního pevného disku, které jsou uloženy jako [stránek](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účty Azure storage](../../storage/common/storage-create-storage-account.md). Následující skript hledá odpojit nespravované disky (objekty BLOB stránky) tak, že prověří hodnotu **LeaseStatus** vlastnost. Když nespravované disku je připojen k virtuálnímu počítači, **LeaseStatus** je nastavena na **uzamčen**. Po odpojit, nespravované disku **LeaseStatus** je nastavena na **Odemknutý**. Skript prověří všechny nespravované disky v všechny účty úložiště Azure v předplatné Azure. Pokud skript vyhledá nespravované disk s **LeaseStatus** vlastnost nastavena na hodnotu **Odemknutý**, skript určí, zda je disk odpojit.

>[!IMPORTANT]
>Nejprve spusťte skript nastavením **deleteUnattachedVHDs** proměnné na hodnotu 0. Tato akce umožňuje vyhledat a zobrazit všechny nepřipojené nespravované virtuální pevné disky.
>
>Po prostudování všech odpojit disků, znovu spusťte skript a nastavte **deleteUnattachedVHDs** proměnné na hodnotu 1. Tato akce umožňuje odstranit všechny nepřipojené nespravované virtuální pevné disky.
>

```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}
```

## <a name="next-steps"></a>Další postup

[Odstranit účet úložiště](../../storage/common/storage-create-storage-account.md)




