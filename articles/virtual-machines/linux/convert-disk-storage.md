---
title: Převést Azure spravované disky úložiště ze standardní, Premium a naopak | Microsoft Docs
description: Postup převedení Azure spravované disky úložiště ze standardní, Premium a naopak, pomocí rozhraní příkazového řádku Azure.
services: virtual-machines-linux
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: c22c2c194cb839c3ec9e3e851768ca19bc6fc443
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2017
ms.locfileid: "23950508"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Převést Azure spravované disky úložiště ze standardní, Premium a naopak

Spravované disky nabízí dvě možnosti úložiště: [Premium](../windows/premium-storage.md) (založené na jednotku SSD) a [standardní](../windows/standard-storage.md) (založené na HDD). Umožňuje snadno přepínat mezi dvě možnosti s minimálními výpadky podle vašim požadavkům na výkon. Tato funkce není k dispozici pro nespravovaná disky. Ale můžete snadno [převést na spravované disky](convert-unmanaged-to-managed-disks.md) snadno přepínat mezi dvě možnosti.

Tento článek ukazuje, jak převést spravované disky z standardní, Premium a naopak pomocí rozhraní příkazového řádku Azure. Pokud je potřeba nainstalovat nebo upgradovat najdete v tématu [nainstalovat Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Než začnete

* Převod vyžaduje restartování virtuálního počítače, takže naplánovat migraci úložiště disků existující období údržby. 
* Pokud používáte nespravované disky, nejprve [převést na spravované disky](convert-unmanaged-to-managed-disks.md) používat v tomto článku přepínat mezi dvě možnosti úložiště. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Převeďte všechny spravované disky virtuálního počítače ze standardní premium a naopak

V následujícím příkladu ukážeme, jak přepínat všechny disky virtuálního počítače z standard do úložiště úrovně premium. Spravované prémiové disky, virtuální počítač vyžaduje použití [velikost virtuálního počítače](sizes.md) který podporuje službu premium storage. Tento příklad také přepne na velikost, která podporuje službu premium storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Převést standardní spravovaných disků na premium a naopak

Pro vývoj/testování úlohy můžete mít směs standard a premium disky na snížení nákladů na vaše. Můžete ji provést upgradem do úložiště úrovně premium, pouze disky, které vyžadují vyšší výkon. V následujícím příkladu ukážeme, jak přepínat jediný disk virtuálního počítače z standard do úložiště úrovně premium a naopak. Spravované prémiové disky, virtuální počítač vyžaduje použití [velikost virtuálního počítače](sizes.md) který podporuje službu premium storage. Tento příklad také přepne na velikost, která podporuje službu premium storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Další kroky

Využít jen pro čtení kopie virtuálního počítače pomocí [snímky](snapshot-copy-managed-disk.md).

