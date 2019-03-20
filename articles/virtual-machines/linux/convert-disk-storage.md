---
title: Převést Azure spravované diskové úložiště úroveň ze Standard na Premium a Premium na Standard | Dokumentace Microsoftu
description: Převést Azure spravovaného disky úložiště úroveň ze Standard na Premium a Premium na Standard pomocí rozhraní příkazového řádku Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 18730f662f36000e1efc826c35bebde79dbf0e79
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013614"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Převést Azure spravované diskové úložiště úroveň ze Standard na Premium a Premium na Standard

Existují čtyři [typy disků](disks-types.md) Azure spravované disky: Azure Ultra diskové úložiště, Premium SSD, SSD na úrovni Standard a Standard pevný disk. Můžete snadno přepínat mezi Premium SSD, SSD na úrovni Standard a standardní pevný disk, na základě vašich potřeb výkonu s malý výpadek. Tato funkce není podporována pro nespravované disky nebo Ultra diskové úložiště. Ale můžete snadno [převést nespravované na spravované disky](convert-unmanaged-to-managed-disks.md) mohli přepínat mezi typů disků nevidí.

Tento článek ukazuje, jak převést spravovaných disků z úrovně Standard na Premium a Premium na Standard s využitím rozhraní příkazového řádku Azure. Pro instalaci nebo upgrade nástroj, najdete v článku [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Před zahájením

* Převod disku vyžaduje restartování virtuálního počítače (VM), proto naplánujte migraci diskové úložiště během už existujícího časového období údržby.
* Pro nespravované disky první [převod na managed disks](convert-unmanaged-to-managed-disks.md) tak můžete přepínat mezi možnostmi úložiště.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Přepnout všechny spravované disky virtuálních počítačů mezi úrovní Premium a Standard

Tento příklad ukazuje, jak převést všechny disky Virtuálního počítače z úrovně Standard na Premium storage nebo z úrovně Premium na Standard storage. Pokud chcete použít spravované disky úrovně Premium, musíte použít váš virtuální počítač [velikost virtuálního počítače](sizes.md) , který podporuje storage úrovně Premium. Tento příklad také přepíná na hodnotu, která podporuje službu Premium storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the SKU of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the SKU of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Přepnout jednotlivé spravované disky mezi úrovněmi Standard a Premium

Pro úlohy pro vývoj/testování můžete chtít obsahovat kombinaci disků Standard a Premium pro snížení nákladů. Můžete upgradovat jenom disky, které je třeba vyšší výkon. Tento příklad ukazuje, jak převést jeden disk virtuálního počítače z úrovně Standard na Premium storage nebo z úrovně Premium na Standard storage. Pokud chcete použít spravované disky úrovně Premium, musíte použít váš virtuální počítač [velikost virtuálního počítače](sizes.md) , který podporuje storage úrovně Premium. Tento příklad také přepíná na hodnotu, která podporuje službu Premium storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports Premium storage 
#Skip this step if converting storage from Premium to Standard
az vm resize --ids $vmId --size $size

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Přepnout mezi standardní disků HDD a SDD standardní spravované disky

Tento příklad ukazuje, jak převést jeden disk virtuálního počítače ze standardního pevného disku na SSD na úrovni Standard nebo z SSD na úrovni Standard na standardní HDD.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM ID 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the SKU
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Přepnout spravované disky mezi úrovněmi Standard a Premium na webu Azure portal

Postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte virtuální počítač ze seznamu **virtuálních počítačů**.
3. Pokud není virtuální počítač zastavený, vyberte **Zastavit** v horní části virtuální počítač **přehled** podokno a vyčkat, než virtuální počítač zastavit.
4. V podokně pro virtuální počítač vyberte **disky** z nabídky.
5. Vyberte disk, který má být převeden.
6. Vyberte **konfigurace** z nabídky.
7. Změnit **typ účtu** z **standardní HDD** k **Premium SSD** nebo z **Premium SSD** k **standardní HDD**.
8. Vyberte **Uložit**a zavřít podokno disku.

Aktualizovat typ disku je okamžité. Váš virtuální počítač můžete restartovat po převodu.

## <a name="next-steps"></a>Další postup

Vytvořit kopii jen pro čtení virtuálního počítače pomocí [snímky](snapshot-copy-managed-disk.md).
