---
title: Vytvoření virtuální sítě – rychlý Start – Azure CLI
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu se dozvíte, jak vytvořit virtuální síť pomocí Azure CLI. Virtuální síť umožňuje prostředkům Azure komunikovat mezi sebou a s internetem.
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0795404c2dc5377d60896863f6a088c4b2ffd1ad
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060784"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Rychlý start: Vytvoření virtuální sítě pomocí Azure CLI

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem. 

V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Pak se k virtuálním počítačům připojíte z Internetu a soukromě komunikujte přes novou virtuální síť.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě

Než budete moct vytvořit virtuální síť, musíte vytvořit skupinu prostředků, která bude hostovat virtuální síť. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Tento příklad vytvoří skupinu prostředků s názvem **CreateVNetQS-RG** v umístění **Eastus** :

```azurecli-interactive
az group create \
    --name CreateVNetQS-rg \
    --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Tento příklad vytvoří výchozí virtuální síť s názvem **myVNet** s jednou podsítí s názvem **Default**:

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group CreateVNetQS-rg \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). 

Pokud klíče SSH ve výchozím umístění klíče ještě neexistují, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. 

`--no-wait`Možnost vytvoří virtuální počítač na pozadí. Můžete pokračovat k dalšímu kroku. 

Tento příklad vytvoří virtuální počítač s názvem **myVM1**:

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --public-ip-address myPublicIP-myVM1 \
  --no-wait
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Použili jste `--no-wait` možnost v předchozím kroku. Můžete pokračovat a vytvořit druhý virtuální počítač s názvem **myVM2**.

```azurecli-interactive
az vm create \
  --resource-group CreateVNetQS-rg \
  --name myVM2 \
  --image UbuntuLTS \
  --public-ip-address myPublicIP-myVM2 \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Výstupní zpráva Azure CLI

Vytvoření virtuálních počítačů trvá několik minut. Po vytvoření virtuálních počítačů Azure CLI vrátí výstup podobný tomuto:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/CreateVNetQS-rg/providers/Microsoft.Compute/virtualMachines/myVM2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "CreateVNetQS-rg"
  "zones": ""
}
```

## <a name="vm-public-ip"></a>Veřejná IP adresa virtuálního počítače

Pokud chcete získat veřejnou IP adresu **myVM2**, použijte příkaz [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

```azurecli-interactive
az network public-ip show \
  --resource-group CreateVNetQS-rg  \
  --name myPublicIP-myVM2 \
  --query ipAddress \
  --output tsv
```

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

V tomto příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou vašeho virtuálního počítače s **myVM2** :

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Pokud chcete potvrdit privátní komunikaci mezi virtuálními počítači **myVM2** a **myVM1** , zadejte tento příkaz:

```bash
ping myVM1 -c 4
```

Dostanete čtyři odpovědi od *10.0.0.4*.

Ukončete relaci SSH s virtuálním počítačem s **myVM2** .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [AZ Group Delete](/cli/azure/group#az_group_delete) :

```azurecli-interactive
az group delete \
    --name CreateVNetQS-rg \
    --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu: 

* Vytvořili jste výchozí virtuální síť a dva virtuální počítače. 
* Připojili jste se k jednomu virtuálnímu počítači z Internetu a komunikovali soukromě mezi těmito dvěma virtuálními počítači.

Privátní komunikace mezi virtuálními počítači není ve virtuální síti omezená. 

V dalším článku se dozvíte víc o konfiguraci různých typů síťových komunikací virtuálních počítačů:
> [!div class="nextstepaction"]
> [Filtrování provozu sítě](tutorial-filter-network-traffic.md)
