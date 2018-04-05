---
title: Vytvoření zoned virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure | Microsoft Docs
description: Vytvoření virtuálního počítače s Linuxem v zóně dostupnosti pomocí Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: cfc3fbf5432108222ee7941d92d78e49d3eaed78
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Vytvořit virtuální počítač s Linuxem v zóně dostupnosti pomocí Azure CLI

Tento článek obsahuje kroky prostřednictvím rozhraní příkazového řádku Azure k vytvoření virtuálního počítače s Linuxem v zóně Azure dostupnosti. [Zóna dostupnosti](../../availability-zones/az-overview.md) je fyzicky oddělená zóna v oblasti Azure. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Pokud chcete použít dostupnosti zóny, vytvoření virtuálního počítače v prostředí [podporované oblasti Azure](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

Ujistěte se, že jste nainstalovali nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) a přihlášený k účtu Azure s [az přihlášení](/cli/azure/reference-index#az_login).


## <a name="check-vm-sku-availability"></a>Kontrola dostupnosti skladových položek virtuálních počítačů
Dostupnost velikostí virtuálních počítačů (neboli skladových položek) se může lišit podle oblasti a zóny. Jako pomůcku při plánování použití zón dostupnosti můžete zobrazit seznam dostupných SKU virtuálních počítačů podle zóny a oblasti Azure. Díky tomu se zajistí, že vyberete odpovídající velikost virtuálního počítače a získáte požadovanou odolnost napříč zónami. Další informace o různých velikostech a typech virtuálních počítačů najdete v [přehledu velikostí virtuálních počítačů](sizes.md).

Můžete zobrazit dostupné edice virtuálních počítačů s [seznamu virtuálních počítačů az-SKU](/cli/azure/vm#az_vm_list_skus) příkaz. Následující příklad zobrazí seznam dostupných skladových položek virtuálních počítačů v oblasti *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

Výstup se podobá následujícímu zhuštěnému příkladu, který ukazuje zóny dostupnosti, ve kterých jsou dostupné jednotlivé velikosti virtuálních počítačů:

```azurecli
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create).  

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače. V tomto příkladu skupinu prostředků s názvem *myResourceGroupVM* je vytvořen v *eastus2* oblast. Východní USA 2 je jedním z oblasti Azure, které podporuje dostupnost zóny.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Skupina prostředků je zadána při vytváření nebo úpravách virtuální počítač, který v tomto článku si můžete prohlédnout.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). 

Při vytváření virtuálního počítače je k dispozici několik možností, jako jsou image operačního systému, velikost disku a přihlašovací údaje pro správu. V tomto příkladu se vytvoří virtuální počítač s názvem *myVM*, na kterém poběží Ubuntu Server. Vytvoření virtuálního počítače v zóně dostupnosti *1*. Ve výchozím nastavení, je virtuální počítač vytvořený v *Standard_DS1_v2* velikost.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys --zone 1
```

Vytvoření virtuálního počítače může několik minut trvat. Po vytvoření virtuálního počítače o něm Azure CLI vypíše informace. Poznamenejte si `zones` hodnotu, která určuje dostupnosti zónu, ve kterém je virtuální počítač spuštěný. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potvrďte zónu pro spravovaných disků a IP adresy

Po nasazení virtuálního počítače v zóně dostupnosti pro virtuální počítač se spravovaným diskem se vytvoří ve stejné zóně dostupnosti. Ve výchozím nastavení je veřejnou IP adresu vytvořen také v této zóně. Následující příklady získat informace o těchto prostředcích.

K ověření, zda je disk spravované Virtuálního počítače v zóně dostupnosti, použijte [az virtuálních počítačů zobrazit](/cli/azure/vm#az_vm_show) příkaz vrátí id disku. V tomto příkladu je id disku uložené v proměnné, která se používá v pozdější fázi. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Teď můžete získat informace o spravovaných disků:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Výstup ukazuje, že spravovaný disk je ve stejné zóně dostupnosti jako virtuální počítač:

```azurecli
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Použití [az virtuálních počítačů seznamu ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses) příkaz vrátí název prostředek veřejné IP adresy v *Můjvp*. V tomto příkladu je název uložené v proměnné, která se používá v pozdější fázi.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Teď můžete získat informace o IP adrese:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Výstup ukazuje, že IP adresa je v zóně dostupnosti jako virtuální počítač:

```azurecli
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili vytvoření virtuálního počítače v zóně dostupnosti. Přečtěte si další informace o [oblastech a dostupnosti](regions-and-availability.md) pro virtuální počítače Azure.




