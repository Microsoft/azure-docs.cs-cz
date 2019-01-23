---
title: Vyhledat a odstranit nepřipojené spravovaných a nespravovaných disků v Azure | Dokumentace Microsoftu
description: Jak najít a odstranit nepřipojené spravovaných a nespravovaných (objekty BLOB virtuálních pevných disků nebo stránky) disků v Azure pomocí Azure CLI.
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
ms.component: disks
ms.openlocfilehash: 58a54b7e867b625adf76d6287d5067c326c80f17
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462789"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>Vyhledat a odstranit nepřipojené spravovaných a nespravovaných disků v Azure
Když odstraníte virtuální počítač (VM) v Azure, ve výchozím nastavení, nebudou odstraněny všechny disky, které jsou připojené k virtuálnímu počítači. Tato funkce pomáhá zabránit ztrátě dat v důsledku neúmyslnému odstranění virtuálních počítačů. Po odstranění virtuálního počítače budete i nadále platit za nepřipojené disky. V tomto článku se dozvíte, jak najít a odstranit všechny nepřipojené disky a omezení zbytečných nákladů. 


## <a name="managed-disks-find-and-delete-unattached-disks"></a>Spravované disky: Vyhledat a odstranit nepřipojené disky 

Následující skript hledá nepřipojené [spravované disky](managed-disks-overview.md) porovnáním hodnoty **ManagedBy** vlastnost. Když spravovaný disk je připojený k virtuálnímu počítači, **ManagedBy** vlastnost obsahuje ID prostředku virtuálního počítače. Když spravovaný disk není připojený, **ManagedBy** vlastnost má hodnotu null. Tento skript kontroluje všechny spravované disky v rámci předplatného Azure. Pokud skript vyhledá spravovaného disku s **ManagedBy** nastavenou na hodnotu null, který určuje, zda disk není připojený.

>[!IMPORTANT]
>Nejprve spusťte skript tím, že nastavíte **deleteUnattachedDisks** proměnných na hodnotu 0. Tato akce umožní vyhledat a zobrazit všechny připojené spravované disky.
>
>Poté, co si všechny nepřipojené disky, spusťte znovu skript a nastavit **deleteUnattachedDisks** proměnných na hodnotu 1. Tato akce umožňuje odstranit všechny připojené spravované disky.
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

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Nespravované disky: Vyhledat a odstranit nepřipojené disky 

Nespravované disky jsou soubory virtuálního pevného disku, které jsou uloženy jako [objekty BLOB stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) v [účty služby Azure storage](../../storage/common/storage-create-storage-account.md). Následující skript hledá nepřipojené nespravované disky (objekty BLOB stránky) tím, že kontroluje hodnotu **LeaseStatus** vlastnost. Když nespravovaný disk je připojený k virtuálnímu počítači, **LeaseStatus** je nastavena na **uzamčené**. Když není připojený, nespravovaný disk **LeaseStatus** je nastavena na **Odemknutý**. Skript kontroluje všechny nespravovaných disků ve všech účtech úložiště Azure v rámci předplatného Azure. Pokud skript vyhledá nespravovaný disk s **LeaseStatus** vlastnost nastavena na hodnotu **Odemknutý**, skript určí, zda disk není připojený.

>[!IMPORTANT]
>Nejprve spusťte skript tím, že nastavíte **deleteUnattachedVHDs** proměnných na hodnotu 0. Tato akce umožní vyhledat a zobrazit všechny nepřipojené nespravované virtuální pevné disky.
>
>Poté, co si všechny nepřipojené disky, spusťte znovu skript a nastavit **deleteUnattachedVHDs** proměnných na hodnotu 1. Tato akce umožňuje odstranit všechny nepřipojené nespravované virtuální pevné disky.
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



