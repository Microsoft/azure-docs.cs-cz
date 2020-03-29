---
title: Převod úložiště spravovaných disků mezi standardním a prémiovým diskem SSD
description: Jak převést úložiště spravovaných disků Azure ze standardního na prémiovou nebo prémiovou na standardní pomocí azure cli.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: cd9bb92b3ed86c3a57b5fc70411a4593335acedb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431491"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-or-premium-to-standard"></a>Převod úložiště spravovaných disků Azure ze standardu na Premium nebo Premium na standardní

Existují čtyři typy disků spravovaných disků Azure: Azure ultra SSD (preview), premium SSD, standardní SSD a standardní HDD. Můžete přepínat mezi třemi typy disků GA (premium SSD, standardní SSD a standardní HDD) na základě vašich potřeb výkonu. Ještě nejste schopni přepnout z nebo na ultra SSD, musíte nasadit nový.

Tato funkce není podporována pro nespravované disky. Nespravovaný disk však můžete snadno [převést na spravovaný disk,](convert-unmanaged-to-managed-disks.md) abyste mohli přepínat mezi typy disků.

Tento článek ukazuje, jak převést spravované disky ze standardu na premium nebo premium na standardní pomocí azure cli. Pokud chcete nástroj nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Než začnete

* Převod disku vyžaduje restartování virtuálního počítače (VM), proto naplánujte migraci úložiště disku během již existujícího okna údržby.
* U nespravovaných disků [nejprve převeďte na spravované disky,](convert-unmanaged-to-managed-disks.md) abyste mohli přepínat mezi možnostmi úložiště.


## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Přepnutí všech spravovaných disků virtuálního počítače mezi premium a standard

Tento příklad ukazuje, jak převést všechny disky virtuálního počítače ze standardního úložiště na úložiště Premium nebo z úložiště Premium na standardní. Chcete-li používat disky spravované službou Premium, musí váš virtuální počítač používat [velikost virtuálního počítače,](sizes.md) která podporuje úložiště Premium. Tento příklad se také přepne na velikost, která podporuje úložiště Premium.

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
## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Přepínání jednotlivých spravovaných disků mezi standardem a premium

Pro vaše úlohy pro vývoj a testování můžete chtít mít kombinaci disků Standard a Premium, abyste snížili náklady. Můžete provést upgrade pouze těch disků, které vyžadují lepší výkon. Tento příklad ukazuje, jak převést jeden disk virtuálního počítače ze standardního úložiště na úložiště Premium nebo z úložiště Premium na standardní. Chcete-li používat disky spravované službou Premium, musí váš virtuální počítač používat [velikost virtuálního počítače,](sizes.md) která podporuje úložiště Premium. Tento příklad se také přepne na velikost, která podporuje úložiště Premium.

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

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Přepínání spravovaných disků mezi standardním pevným diskem a standardním diskem SSD

Tento příklad ukazuje, jak převést jeden disk virtuálního počítače ze standardního pevného disku na standardní ssd nebo ze standardního ssd disku na standardní pevný disk.

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

## <a name="switch-managed-disks-between-standard-and-premium-in-azure-portal"></a>Přepínání spravovaných disků mezi Standardem a Premium na webu Azure Portal

Postupujte následovně:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte virtuální počítač ze seznamu **virtuálních počítačů**.
3. Pokud virtuální hotel není zastaven, vyberte **Zastavit** v horní části podokna **Přehled** virtuálního zařízení a počkejte, až se virtuální hod zastaví.
4. V podokně virtuálního počítače vyberte **disky** z nabídky.
5. Vyberte disk, který chcete převést.
6. V nabídce vyberte **Konfigurace.**
7. Změňte **typ účtu** ze **standardního pevného disku** na Premium **SSD** nebo z **Premium SSD** na **Standardní HDD**.
8. Vyberte **Uložit**a zavřete podokno disku.

Aktualizace typu disku je okamžitá. Virtuální počítač můžete po převodu restartovat.

## <a name="next-steps"></a>Další kroky

Vytvořte kopii virtuálního počítače jen pro čtení pomocí [snímků](snapshot-copy-managed-disk.md).
