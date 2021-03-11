---
title: Vytvoření virtuálního počítače v zóně pomocí Azure CLI
description: Vytvoření virtuálního počítače v zóně dostupnosti pomocí Azure CLI
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 04/05/2018
ms.author: cynthn
ms.openlocfilehash: 3405468c64a7130bb0d71543403dd4ce5ef9c241
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558368"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-cli"></a>Vytvoření virtuálního počítače v zóně dostupnosti pomocí Azure CLI

Tento článek popisuje použití Azure CLI k vytvoření virtuálního počítače se systémem Linux v zóně dostupnosti Azure. [Zóna dostupnosti](../../availability-zones/az-overview.md) je fyzicky oddělená zóna v oblasti Azure. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Pokud chcete využít zóny dostupnosti, vytvořte virtuální počítač v [podporované oblasti Azure](../../availability-zones/az-region.md).

Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásili jste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Kontrola dostupnosti skladových položek virtuálních počítačů
Dostupnost velikostí virtuálních počítačů (neboli skladových položek) se může lišit podle oblasti a zóny. Jako pomůcku při plánování použití zón dostupnosti můžete zobrazit seznam dostupných SKU virtuálních počítačů podle zóny a oblasti Azure. Díky tomu se zajistí, že vyberete odpovídající velikost virtuálního počítače a získáte požadovanou odolnost napříč zónami. Další informace o různých velikostech a typech virtuálních počítačů najdete v [přehledu velikostí virtuálních počítačů](../sizes.md).

Dostupné skladové položky virtuálních počítačů můžete zobrazit pomocí příkazu [AZ VM list-SKU](/cli/azure/vm) . Následující příklad zobrazí seznam dostupných skladových položek virtuálních počítačů v oblasti *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

Výstup se podobá následujícímu zhuštěnému příkladu, který ukazuje zóny dostupnosti, ve kterých jsou dostupné jednotlivé velikosti virtuálních počítačů:

```output
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

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače. V tomto příkladu se vytvoří skupina prostředků s názvem *myResourceGroupVM* v oblasti *eastus2* . Východní USA 2 je jednou z oblastí Azure, které podporují zóny dostupnosti.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Skupina prostředků se zadává při vytváření nebo úpravách virtuálních počítačů, které se můžou zobrazit v celém rámci tohoto článku.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). 

Při vytváření virtuálního počítače je k dispozici několik možností, jako jsou image operačního systému, velikost disku a přihlašovací údaje pro správu. V tomto příkladu se vytvoří virtuální počítač s názvem *myVM*, na kterém poběží Ubuntu Server. Virtuální počítač se vytvoří v zóně dostupnosti *1*. Ve výchozím nastavení se virtuální počítač vytvoří ve velikosti *Standard_DS1_v2* .

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

Vytvoření virtuálního počítače může několik minut trvat. Po vytvoření virtuálního počítače o něm Azure CLI vypíše informace. Poznamenejte si `zones` hodnotu, která indikuje zónu dostupnosti, ve které je virtuální počítač spuštěný. 

```output
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

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potvrďte zónu pro spravovaný disk a IP adresu.

Když je virtuální počítač nasazený v zóně dostupnosti, vytvoří se spravovaný disk pro virtuální počítač ve stejné zóně dostupnosti. Ve výchozím nastavení se v této zóně vytvoří také veřejná IP adresa. V následujících příkladech se dozvíte o těchto prostředcích.

Pokud chcete ověřit, jestli je spravovaný disk virtuálního počítače v zóně dostupnosti, vraťte ID disku pomocí příkazu [AZ VM show](/cli/azure/vm) . V tomto příkladu je ID disku uložené v proměnné, která se používá v pozdějším kroku. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Nyní můžete získat informace o spravovaném disku:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Výstup ukazuje, že spravovaný disk je ve stejné zóně dostupnosti jako virtuální počítač:

```output
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

Pomocí příkazu [AZ VM list-IP-addresss](/cli/azure/vm) vraťte název prostředku veřejné IP adresy v *myVM*. V tomto příkladu je název uložen v proměnné, která je použita v pozdějším kroku.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Nyní můžete získat informace o IP adrese:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Výstup ukazuje, že IP adresa je ve stejné zóně dostupnosti jako virtuální počítač:

```output
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

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit virtuální počítač v zóně dostupnosti. Přečtěte si další informace o [dostupnosti](../availability.md) pro virtuální počítače Azure.