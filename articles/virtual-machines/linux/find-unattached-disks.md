---
title: Vyhledejte a odstraňte odpojit spravovanými a nespravovanými disky Azure | Microsoft Docs
description: Postup nalezení a odstranění odpojit spravovaných a nespravovaných (objekty BLOB VHD/stránky) disky systému Azure pomocí rozhraní příkazového řádku Azure.
services: virtual-machines-linux
documentationcenter: ''
author: ramankumarlive
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: ramankum
ms.openlocfilehash: 1718b35aa68937cad4b3ae7e677e300506820bd0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30323249"
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: Vyhledejte a odstraňte odpojit disky 

Nespravované disky jsou soubory virtuálního pevného disku, které jsou uloženy jako [stránek](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účty Azure storage](../../storage/common/storage-create-storage-account.md). Následující skript hledá odpojit nespravované disky (objekty BLOB stránky) tak, že prověří hodnotu **LeaseStatus** vlastnost. Když nespravované disku je připojen k virtuálnímu počítači, **LeaseStatus** je nastavena na **uzamčen**. Po odpojit, nespravované disku **LeaseStatus** je nastavena na **Odemknutý**. Skript prověří všechny nespravované disky v všechny účty úložiště Azure v předplatné Azure. Pokud skript vyhledá nespravované disk s **LeaseStatus** vlastnost nastavena na hodnotu **Odemknutý**, skript určí, zda je disk odpojit.

>[!IMPORTANT]
>Nejprve spusťte skript nastavením **deleteUnattachedVHDs** proměnné na hodnotu 0. Tato akce umožňuje vyhledat a zobrazit všechny nepřipojené nespravované virtuální pevné disky.
>
>Po prostudování všech odpojit disků, znovu spusťte skript a nastavte **deleteUnattachedVHDs** proměnné na hodnotu 1. Tato akce umožňuje odstranit všechny nepřipojené nespravované virtuální pevné disky.
>

```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

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

## <a name="next-steps"></a>Další postup

[Odstranit účet úložiště](../../storage/common/storage-create-storage-account.md)



