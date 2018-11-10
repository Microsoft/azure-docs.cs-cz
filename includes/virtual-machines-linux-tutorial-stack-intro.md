---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: b922b5ea225c61948240e40903ac43f56fde3fb5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226960"
---
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). 

Následující příklad vytvoří virtuální počítač *myVM*, a pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, vytvoří je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Tento příkaz také jako uživatelské jméno správce nastaví *azureuser*. Toto uživatelské jméno použijete později k připojení k virtuálnímu počítači. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu. Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači v dalších krocích.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz 

Ve výchozím nastavení jsou k virtuálním počítačům s Linuxem, které jsou nasazené v Azure, povolená pouze připojení SSH. Protože tento virtuální počítač bude webovým serverem, budete muset otevřít port 80 z internetu. Požadovaný port otevřete pomocí příkazu [az vm open-port](/cli/azure/vm#az_vm_open_port).  
 
```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```
## <a name="ssh-into-your-vm"></a>Připojení SSH k virtuálnímu počítači


Pokud ještě neznáte veřejnou IP adresu svého virtuálního počítače, spusťte příkaz [az network public-ip list](/cli/azure/network/public-ip#list). Tuto IP adresu budete potřebovat v několika dalších krocích.


```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Použijte správnou veřejnou IP adresu vašeho virtuálního počítače. V tomto příkladu je IP adresa *40.68.254.142*. *azureuser* je uživatelské jméno správce, které jste nastavili při vytváření virtuálního počítače.

```bash
ssh azureuser@40.68.254.142
```

