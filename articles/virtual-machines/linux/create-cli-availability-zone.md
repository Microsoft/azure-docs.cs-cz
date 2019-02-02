---
title: Vytvoření zóny virtuálního počítače s Linuxem pomocí Azure CLI | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače s Linuxem v zóně dostupnosti s využitím rozhraní příkazového řádku Azure
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
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 8be3156aaeb9651b97999b7b06fec8034e902c36
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663889"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Vytvoření virtuálního počítače s Linuxem v zóně dostupnosti s využitím rozhraní příkazového řádku Azure

Tento článek prochází jednotlivé kroky k vytvoření virtuálního počítače s Linuxem v zóně dostupnosti Azure pomocí Azure CLI. [Zóna dostupnosti](../../availability-zones/az-overview.md) je fyzicky oddělená zóna v oblasti Azure. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Pokud chcete využít zóny dostupnosti, vytvořte virtuální počítač v [podporované oblasti Azure](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

Ujistěte se, že máte nainstalovanou nejnovější verzi [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) a jste přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).


## <a name="check-vm-sku-availability"></a>Kontrola dostupnosti skladových položek virtuálních počítačů
Dostupnost velikostí virtuálních počítačů (neboli skladových položek) se může lišit podle oblasti a zóny. Jako pomůcku při plánování použití zón dostupnosti můžete zobrazit seznam dostupných SKU virtuálních počítačů podle zóny a oblasti Azure. Díky tomu se zajistí, že vyberete odpovídající velikost virtuálního počítače a získáte požadovanou odolnost napříč zónami. Další informace o různých velikostech a typech virtuálních počítačů najdete v [přehledu velikostí virtuálních počítačů](sizes.md).

Můžete zobrazit dostupné skladové položky virtuálních počítačů s [az vm list-skus](/cli/azure/vm#az_vm_list_skus) příkazu. Následující příklad zobrazí seznam dostupných skladových položek virtuálních počítačů v oblasti *eastus2*:

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

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group).  

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače. V tomto příkladu skupina prostředků s názvem *myResourceGroupVM* se vytvoří v *eastus2* oblasti. Východní USA 2 je jednou z oblasti Azure, které podporují zóny dostupnosti.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Skupina prostředků je určena při vytváření nebo úpravách virtuálního počítače, což uvidíte dále v tomto článku.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). 

Při vytváření virtuálního počítače je k dispozici několik možností, jako jsou image operačního systému, velikost disku a přihlašovací údaje pro správu. V tomto příkladu se vytvoří virtuální počítač s názvem *myVM*, na kterém poběží Ubuntu Server. Vytvoření virtuálního počítače v zóně dostupnosti *1*. Ve výchozím nastavení, se vytvoří virtuální počítač v *Standard_DS1_v2* velikost.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

Vytvoření virtuálního počítače může několik minut trvat. Po vytvoření virtuálního počítače o něm Azure CLI vypíše informace. Poznamenejte si `zones` hodnotu, která určuje zónu dostupnosti, ve kterém je virtuální počítač spuštěný. 

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

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potvrzení zóny pro spravovaný disk a IP adresa

Po nasazení virtuálního počítače v zóně dostupnosti spravovaného disku pro virtuální počítač se vytvoří ve stejné zóně dostupnosti. Ve výchozím nastavení je vytvořen v této oblasti také veřejnou IP adresu. Následující příklady získat informace o těchto prostředcích.

Chcete-li ověřit, že spravovaný disk Virtuálního počítače je v zóně dostupnosti, použijte [az vm show](/cli/azure/vm#az_vm_show) příkaz pro zjištění id disku. V tomto příkladu je id disku uložené v proměnné, která se používá v pozdější fázi. 

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

Použití [az vm list-ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses) příkaz, který vrátí název prostředku veřejné IP adresy v *myVM*. V tomto příkladu je název uložen v proměnné, která se používá v pozdější fázi.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Teď můžete získat informace o IP adrese:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Výstup ukazuje, že IP adresa je ve stejné zóně dostupnosti jako virtuální počítač:

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

V tomto článku jste zjistili, jak vytvořit virtuální počítač v zóně dostupnosti. Přečtěte si další informace o [oblastech a dostupnosti](regions-and-availability.md) pro virtuální počítače Azure.




