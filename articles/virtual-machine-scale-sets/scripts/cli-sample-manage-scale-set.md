---
title: Ukázka Azure CLI pro správu sady škálování virtuálních počítačů
description: V této ukázce se dozvíte, jak přidat disky do sady škálování virtuálních počítačů. Můžete upgradovat disky a přidávat virtuální počítače do ověřování Azure AD.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672605"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Vytvoření a Správa sady škálování virtuálních počítačů

Pomocí těchto ukázkových příkazů můžete pomocí rozhraní příkazového řádku Azure prototypovat sadu škálování virtuálního počítače.

Tyto ukázkové příkazy demonstrují následující operace:

* Vytvoří škálovací sadu virtuálních počítačů.
* Přidejte a upgradujte nové nebo existující disky do sady škálování nebo do instance sady.
* Přidejte sadu škálování nastavenou na Azure Active Directory (Azure AD) ověřování.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Ukázkové příkazy

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Po přidání nového datového disku naformátujte a připojte disk. Informace o virtuálních počítačích s Windows najdete v tématu [připojení spravovaného datového disku k virtuálnímu počítači s Windows pomocí Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md). Virtuální počítače se systémem Linux najdete v tématu [Přidání disku do virtuálního počítače se systémem Linux](../../virtual-machines/linux/add-disk.md).

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Chcete-li použít rozšířený disk, rozbalte příslušný oddíl. Další informace najdete v tématu [rozšíření diskového oddílu a systému souborů](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem).

V tomto příkladu se změnila velikost datového disku. Stejný postup můžete použít k aktualizaci disku s operačním systémem. Další informace o virtuálním počítači s Windows najdete v tématu [postup rozšíření jednotky operačního systému virtuálního počítače](../../virtual-machines/windows/expand-os-disk.md). Další informace o virtuálních počítačích se systémem Linux najdete v tématu věnovaném [rozbalení virtuálních pevných disků na virtuálním počítači se systémem Linux pomocí Azure CLI](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po použití těchto příkazů spuštěním následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Odkazy na Azure CLI použité v tomto článku

* [AZ disk DELETE](/cli/azure/disk#az_disk_delete)
* [AZ disk list](/cli/azure/disk#az_disk_list)
* [AZ disk Update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [AZ Virtual Machine Scale set unallocate](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [AZ VMSS disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [AZ VMSS identity Assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [AZ VMSS list-Instances](/cli/azure/vmss#az_vmss_list_instances)
* [AZ VMSS restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)