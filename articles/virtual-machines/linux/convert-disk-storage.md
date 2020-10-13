---
title: Převod úložiště spravovaných disků mezi Standard a SSD
description: Jak převést úložiště Azure Managed disks z úrovně Standard na Premium nebo Premium na standard pomocí Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 10674d15a7515b01b0df6cf37bce89f153cb9b0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870681"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Převod úložiště Azure Managed disks z úrovně Standard na Premium nebo Premium na standard

Existují čtyři typy disků Azure Managed disks: disky Azure Ultra, Premium SSD, Standard SSD a standardní HDD. Na základě vašich požadavků na výkon můžete přepínat mezi třemi typy disků GA (Premium SSD, Standard SSD a Standard HDD). Ještě nejste schopni přepnout z nebo na disk Ultra, musíte nasadit nový.

Tato funkce není podporovaná pro nespravované disky. Nespravovaný disk ale můžete snadno [převést na spravovaný disk](convert-unmanaged-to-managed-disks.md) , aby bylo možné přepínat mezi typy disků.

V tomto článku se dozvíte, jak převést spravované disky z úrovně Standard na Premium nebo Premium na standard pomocí Azure CLI. Informace o instalaci nebo upgradu tohoto nástroje najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Než začnete

* Převod disku vyžaduje restartování virtuálního počítače, proto naplánujte migraci diskového úložiště během již existujícího časového období údržby.
* U nespravovaných disků nejprve [převeďte na spravované disky](convert-unmanaged-to-managed-disks.md) , abyste mohli přepínat mezi možnostmi úložiště.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Přepínání všech spravovaných disků virtuálního počítače v rámci úrovně Premium a Standard

Tento příklad ukazuje, jak převést všechny disky virtuálního počítače z úrovně Standard na Premium Storage nebo z úrovně Premium na úložiště Standard. Pokud chcete používat službu Premium Managed disks, musí mít virtuální počítač [Velikost virtuálního počítače](../sizes.md) , která podporuje Premium Storage. Tento příklad také přepíná na velikost, která podporuje Premium Storage.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Přepnout jednotlivé spravované disky mezi standardem a Premium

Pro vaše úlohy pro vývoj a testování můžete potřebovat kombinaci standardních a prémiových disků, abyste snížili náklady. Můžete si vybrat, jestli chcete upgradovat jenom ty disky, které potřebují lepší výkon. Tento příklad ukazuje, jak převést jeden disk virtuálního počítače z úrovně Standard na Premium Storage nebo z úrovně Premium na úložiště úrovně Standard. Pokud chcete používat službu Premium Managed disks, musí mít virtuální počítač [Velikost virtuálního počítače](../sizes.md) , která podporuje Premium Storage. Tento příklad také přepíná na velikost, která podporuje Premium Storage.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Přepínat spravované disky mezi HDD úrovně Standard a SSD úrovně Standard

Tento příklad ukazuje, jak převést jeden disk virtuálního počítače z HDD úrovně Standard na SSD úrovně Standard nebo z SSD úrovně Standard na HDD úrovně Standard.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Přepne spravované disky mezi Standard a Premium v Azure Portal

Postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. V seznamu **virtuálních počítačů**vyberte virtuální počítač.
3. Pokud se virtuální počítač nezastavil, v horní části podokna **přehledu** virtuálních počítačů vyberte **zastavit** a počkejte, než se virtuální počítač zastaví.
4. V podokně pro virtuální počítač vyberte z nabídky **disky** .
5. Vyberte disk, který chcete převést.
6. V nabídce vyberte **Konfigurace** .
7. Změňte **typ účtu** z **HDD úrovně Standard** na **SSD úrovně Premium** nebo z **SSD úrovně Premium** na **HDD úrovně Standard**.
8. Vyberte **Uložit**a zavřete podokno disk.

Aktualizace typu disku je okamžitá. Po převodu můžete restartovat virtuální počítač.

## <a name="next-steps"></a>Další kroky

Vytvořte kopii virtuálního počítače jen pro čtení pomocí [snímků](snapshot-copy-managed-disk.md).