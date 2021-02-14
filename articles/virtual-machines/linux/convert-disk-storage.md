---
title: Převod úložiště spravovaných disků mezi různými typy disků pomocí Azure CLI
description: Jak převést spravované disky Azure mezi různými typy disků pomocí Azure CLI.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/13/2021
ms.author: albecker
ms.subservice: disks
ms.openlocfilehash: ec6d5b10b4c2336ba7fd221b43a7dbf43ed5cee1
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516817"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Převod úložiště Azure Managed disks z úrovně Standard na Premium nebo Premium na standard

Existují čtyři typy disků Azure Managed disks: disky Azure Ultra, Premium SSD, Standard SSD a standardní HDD. V závislosti na vašich požadavcích na výkon můžete přepínat mezi Premium SSD, Standard SSD a standardním pevným diskem. Ještě nejste schopni přepnout z nebo na disk Ultra, musíte nasadit nový.

Tato funkce není podporovaná pro nespravované disky. Nespravovaný disk ale můžete snadno [převést na spravovaný disk](convert-unmanaged-to-managed-disks.md) , aby bylo možné přepínat mezi typy disků.

V tomto článku se dozvíte, jak převést spravované disky z jednoho typu disku na jiný pomocí Azure CLI. Informace o instalaci nebo upgradu tohoto nástroje najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Než začnete

* Převod disku vyžaduje restartování virtuálního počítače, proto naplánujte migraci diskového úložiště během již existujícího časového období údržby.
* U nespravovaných disků nejprve [převeďte na spravované disky](convert-unmanaged-to-managed-disks.md) , abyste mohli přepínat mezi možnostmi úložiště.


## <a name="switch-all-managed-disks-of-a-vm-between-from-one-account-to-another"></a>Přepínání všech spravovaných disků virtuálního počítače z jednoho účtu na jiný

Tento příklad ukazuje, jak převést všechny disky virtuálního počítače na Premium Storage. Když ale v tomto příkladu změníte proměnnou SKU, můžete převést typ disků virtuálního počítače na standardní SSD nebo standardní pevný disk. Mějte na paměti, že pokud chcete používat službu Premium Managed disks, musí mít virtuální počítač [Velikost virtuálního počítače](../sizes.md) , která podporuje Premium Storage. Tento příklad také přepíná na velikost, která podporuje Premium Storage.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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
## <a name="switch-individual-managed-disks-from-one-disk-type-to-another"></a>Přepnout jednotlivé spravované disky z jednoho typu na jiný disk

Pro vaše úlohy pro vývoj a testování můžete potřebovat kombinaci standardních a prémiových disků, abyste snížili náklady. Můžete si vybrat, jestli chcete upgradovat jenom ty disky, které potřebují lepší výkon. Tento příklad ukazuje, jak převést jeden disk virtuálního počítače z úrovně Standard na Premium Storage. Když ale v tomto příkladu změníte proměnnou SKU, můžete převést typ disků virtuálního počítače na standardní SSD nebo standardní pevný disk. Pokud chcete používat službu Premium Managed disks, musí mít virtuální počítač [Velikost virtuálního počítače](../sizes.md) , která podporuje Premium Storage. Tento příklad také přepíná na velikost, která podporuje Premium Storage.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from Standard to Premium
size='Standard_DS2_v2'

#Choose between Standard_LRS, StandardSSD_LRS and Premium_LRS based on your scenario
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

## <a name="switch-managed-disks-from-one-disk-type-to-another"></a>Přepnout spravované disky z jednoho typu na jiný disk

Postupujte takto:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. V seznamu **virtuálních počítačů** vyberte virtuální počítač.
3. Pokud se virtuální počítač nezastavil, v horní části podokna **přehledu** virtuálních počítačů vyberte **zastavit** a počkejte, než se virtuální počítač zastaví.
4. V podokně pro virtuální počítač vyberte z nabídky **disky** .
5. Vyberte disk, který chcete převést.
6. V nabídce vyberte **Konfigurace** .
7. Z původního typu disku změňte **typ účtu** na požadovaný typ disku.
8. Vyberte **Uložit** a zavřete podokno disk.

Aktualizace typu disku je okamžitá. Po převodu můžete restartovat virtuální počítač.

## <a name="next-steps"></a>Další kroky

Vytvořte kopii virtuálního počítače jen pro čtení pomocí [snímků](snapshot-copy-managed-disk.md).