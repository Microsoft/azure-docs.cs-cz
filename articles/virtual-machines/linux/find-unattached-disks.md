---
title: Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure
description: Jak najít a odstranit nepřipojené a nespravované disky Azure spravované a nespravované (VHD/Page BLOBS) pomocí Azure CLI.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/30/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 87fe277bbd2fa618d43ce3274c1d2c05a5d7b396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84660165"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks-using-the-azure-cli"></a>Vyhledání a odstranění nepřipojených spravovaných a nespravovaných disků Azure pomocí Azure CLI
Při odstranění virtuálního počítače v Azure se ve výchozím nastavení neodstraní všechny disky připojené k virtuálnímu počítači. Tato funkce pomáhá zabránit ztrátě dat z důvodu neúmyslného odstranění virtuálních počítačů. Po odstranění virtuálního počítače budete platit za nepřipojené disky. V tomto článku se dozvíte, jak najít a odstranit všechny nepřipojené disky a omezit zbytečné náklady. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Spravované disky: najít a odstranit nepřipojené disky 

Následující skript vyhledá nepřipojené [spravované disky](managed-disks-overview.md) zkoumáním hodnoty vlastnosti **ManagedBy** . Po připojení spravovaného disku k virtuálnímu počítači vlastnost **ManagedBy** obsahuje ID prostředku virtuálního počítače. Pokud je nepřipojený spravovaný disk, vlastnost **ManagedBy** má hodnotu null. Skript prověřuje všechny spravované disky v rámci předplatného Azure. Když skript vyhledá spravovaný disk s vlastností **ManagedBy** nastavenou na hodnotu null, skript určí, že disk není připojený.

>[!IMPORTANT]
>Nejdřív spusťte skript nastavením proměnné **deleteUnattachedDisks** na hodnotu 0. Tato akce vám umožní najít a zobrazit všechny nepřipojené spravované disky.
>
>Po kontrole všech nepřipojených disků spusťte skript znovu a nastavte proměnnou **deleteUnattachedDisks** na hodnotu 1. Tato akce umožňuje odstranit všechny nepřipojené spravované disky.
>

```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done
```

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: najít a odstranit nepřipojené disky 

Nespravované disky jsou soubory VHD, které se ukládají jako [objekty blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účtech úložiště Azure](../../storage/common/storage-account-overview.md). Následující skript vyhledá nepřipojené nespravované disky (objekty blob stránky) prozkoumáním hodnoty vlastnosti **LeaseStatus** . Pokud je k virtuálnímu počítači připojen nespravovaný disk, vlastnost **LeaseStatus** je nastavena na hodnotu **Uzamčeno**. Když není připojen nespravovaný disk, vlastnost **LeaseStatus** je nastavena na hodnotu **odemčeno**. Skript prověřuje všechny nespravované disky ve všech účtech úložiště Azure v rámci předplatného Azure. Když skript najde nespravovaný disk s vlastností **LeaseStatus** nastavenou na **odemknutý**, skript určí, že disk není připojený.

>[!IMPORTANT]
>Nejdřív spusťte skript nastavením proměnné **deleteUnattachedVHDs** na hodnotu 0. Tato akce vám umožní najít a zobrazit všechny nepřipojené nepřipojené nespravované virtuální pevné disky.
>
>Po kontrole všech nepřipojených disků spusťte skript znovu a nastavte proměnnou **deleteUnattachedVHDs** na hodnotu 1. Tato akce umožňuje odstranit všechny nepřipojené nepřipojené nespravované virtuální pevné disky.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=0

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [odstranění účtu úložiště](../../storage/common/storage-account-create.md#delete-a-storage-account).


