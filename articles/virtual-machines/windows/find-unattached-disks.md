---
title: Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure
description: Jak najít a odstranit nepřipojené disky Azure spravované a nespravované (VHD/stránky blobs) pomocí Azure PowerShellu.
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cac192186c91259a5573dc27442137729816991a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869597"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure

Když odstraníte virtuální počítač (VM) v Azure, ve výchozím nastavení se neodstraní všechny disky připojené k virtuálnímu počítači. Tato funkce pomáhá zabránit ztrátě dat v důsledku neúmyslného odstranění virtuálních stránek. Po odstranění virtuálního počítače budete nadále platit za nepřipojené disky. Tento článek ukazuje, jak najít a odstranit všechny nepřipojené disky a snížit zbytečné náklady.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Spravované disky: Hledání a odstraňování nepřipojených disků

Následující skript hledá nepřipojené [spravované disky](managed-disks-overview.md) kontrolou hodnoty **ManagedBy** vlastnost. Když je spravovaný disk připojený k virtuálnímu počítače, vlastnost **ManagedBy** obsahuje ID prostředku virtuálního počítače. Pokud je spravovaný disk odpojen, vlastnost **ManagedBy** má hodnotu null. Skript zkontroluje všechny spravované disky v předplatném Azure. Když skript vyhledá spravovaný disk s vlastností **ManagedBy** nastavenou na hodnotu null, skript určí, že disk není připojen.

>[!IMPORTANT]
>Nejprve spusťte skript nastavením proměnné **deleteUnattachedDisks** na 0. Tato akce umožňuje najít a zobrazit všechny nepřipojené spravované disky.
>
>Po kontrole všech nepřipojených disků spusťte skript znovu a nastavte proměnnou **deleteUnattachedDisks** na 1. Tato akce umožňuje odstranit všechny nepřipojené spravované disky.

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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: Vyhledání a odstranění nepřipojených disků

Nespravované disky jsou soubory Virtuálního pevného disku, které jsou uloženy jako [objekty BLOB stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účtech úložiště Azure](../../storage/common/storage-create-storage-account.md). Následující skript vyhledá nepřipojené nespravované disky (objekty BLOB stránky) kontrolou hodnoty vlastnosti **LeaseStatus.** Pokud je k virtuálnímu počítače připojen nespravovaný disk, je vlastnost **LeaseStatus** nastavena na **uzamčeno**. Pokud není připojen nespravovaný disk, je vlastnost **LeaseStatus** nastavena na **odemčeno**. Skript zkoumá všechny nespravované disky ve všech účtech úložiště Azure v předplatném Azure. Pokud skript vyhledá nespravovaný disk s vlastností **LeaseStatus** nastavenou na **Unlocked**, skript určí, že disk není připojen.

>[!IMPORTANT]
>Nejprve spusťte skript nastavením **deleteUnattachedVHDs** proměnné 0. Tato akce umožňuje najít a zobrazit všechny nepřipojené nespravované virtuální disky.
>
>Po kontrole všech nepřipojených disků spusťte skript znovu a nastavte proměnnou **deleteUnattachedVHDs** na 1. Tato akce umožňuje odstranit všechny nepřipojené nespravované virtuální disponitova.

```azurepowershell-interactive
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=0
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
                    if($deleteUnattachedVHDs -eq 1){
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

Další informace naleznete v [tématech Odstranění účtu úložiště](../../storage/common/storage-create-storage-account.md) a [Identifikace osamocených disků pomocí prostředí PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)
