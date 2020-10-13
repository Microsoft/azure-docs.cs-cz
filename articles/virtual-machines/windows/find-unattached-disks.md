---
title: Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure
description: Jak najít a odstranit nepřipojené disky spravované v Azure a nespravované (VHD/Page BLOB) pomocí Azure PowerShell.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4f217a53c23df4f161207aaceb528680ddcddbe7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972794"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure

Při odstranění virtuálního počítače v Azure se ve výchozím nastavení neodstraní všechny disky připojené k virtuálnímu počítači. Tato funkce pomáhá zabránit ztrátě dat z důvodu neúmyslného odstranění virtuálních počítačů. Po odstranění virtuálního počítače budete platit za nepřipojené disky. V tomto článku se dozvíte, jak najít a odstranit všechny nepřipojené disky a omezit zbytečné náklady.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Spravované disky: najít a odstranit nepřipojené disky

Následující skript vyhledá nepřipojené [spravované disky](../managed-disks-overview.md) zkoumáním hodnoty vlastnosti **ManagedBy** . Po připojení spravovaného disku k virtuálnímu počítači vlastnost **ManagedBy** obsahuje ID prostředku virtuálního počítače. Pokud je nepřipojený spravovaný disk, vlastnost **ManagedBy** má hodnotu null. Skript prověřuje všechny spravované disky v rámci předplatného Azure. Když skript vyhledá spravovaný disk s vlastností **ManagedBy** nastavenou na hodnotu null, skript určí, že disk není připojený.

>[!IMPORTANT]
>Nejdřív spusťte skript nastavením proměnné **deleteUnattachedDisks** na hodnotu 0. Tato akce vám umožní najít a zobrazit všechny nepřipojené spravované disky.
>
>Po kontrole všech nepřipojených disků spusťte skript znovu a nastavte proměnnou **deleteUnattachedDisks** na hodnotu 1. Tato akce umožňuje odstranit všechny nepřipojené spravované disky.

```azurepowershell-interactive
# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0
$managedDisks = Get-AzDisk
foreach ($md in $managedDisks) {
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){
        if($deleteUnattachedDisks -eq 1){
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"
            $md | Remove-AzDisk -Force
            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "
        }else{
            $md.Id
        }
    }
 }
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: najít a odstranit nepřipojené disky

Nespravované disky jsou soubory VHD, které se ukládají jako [objekty blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účtech úložiště Azure](../../storage/common/storage-account-overview.md). Následující skript vyhledá nepřipojené nespravované disky (objekty blob stránky) prozkoumáním hodnoty vlastnosti **LeaseStatus** . Pokud je k virtuálnímu počítači připojen nespravovaný disk, vlastnost **LeaseStatus** je nastavena na hodnotu **Uzamčeno**. Když není připojen nespravovaný disk, vlastnost **LeaseStatus** je nastavena na hodnotu **odemčeno**. Skript prověřuje všechny nespravované disky ve všech účtech úložiště Azure v rámci předplatného Azure. Když skript najde nespravovaný disk s vlastností **LeaseStatus** nastavenou na **odemknutý**, skript určí, že disk není připojený.

>[!IMPORTANT]
>Nejdřív spusťte skript nastavením proměnné **deleteUnattachedVHDs** na `$false` . Tato akce vám umožní najít a zobrazit všechny nepřipojené nepřipojené nespravované virtuální pevné disky.
>
>Po kontrole všech nepřipojených disků spusťte skript znovu a nastavte proměnnou **deleteUnattachedVHDs** na `$true` . Tato akce umožňuje odstranit všechny nepřipojené nepřipojené nespravované virtuální pevné disky.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=$true if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=$false if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=$false
$storageAccounts = Get-AzStorageAccount
foreach($storageAccount in $storageAccounts){
    $storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value
    $context = New-AzStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey
    $containers = Get-AzStorageContainer -Context $context
    foreach($container in $containers){
        $blobs = Get-AzStorageBlob -Container $container.Name -Context $context
        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
                    if($deleteUnattachedVHDs){
                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                        $_ | Remove-AzStorageBlob -Force
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

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [odstranění účtu úložiště](../../storage/common/storage-account-create.md#delete-a-storage-account) a [Identifikace osamocených disků pomocí prostředí PowerShell](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell) .