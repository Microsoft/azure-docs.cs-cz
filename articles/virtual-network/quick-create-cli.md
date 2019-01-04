---
title: Vytvoření virtuální sítě – rychlý start – Azure CLI
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu zjistíte, jak vytvořit virtuální síť pomocí Azure CLI. Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s Internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: jdial
ms.openlocfilehash: 650c90ff5c193896806ded50e5b7c9ed22b3afde
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013600"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Rychlý start: Vytvoření virtuální sítě pomocí Azure CLI

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače (VM), komunikovat soukromě mezi sebou a s Internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Potom z Internetu připojit k virtuálním počítačům a komunikovat soukromě mezi za novou virtuální síť.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure místně místo toho tento rychlý start vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním `az --version`. Zobrazit [instalace Azure CLI](/cli/azure/install-azure-cli) pro instalaci nebo upgradu informace.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Vytvořte skupinu prostředků a virtuální sítě

Než vytvoříte virtuální síť, je nutné vytvořit skupinu prostředků k hostování ve virtuální síti. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v *eastus* umístění:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí *výchozí*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Pomocí možnosti `--no-wait` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku. Tento příklad vytvoří virtuální počítač s názvem *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Protože jste použili `--no-wait` možnost v předchozím kroku, vám pokračujte a vytvořte druhý virtuální počítač s názvem *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI výstupní zpráva

Vytvoření virtuálních počítačů trvá několik minut. Když Azure vytvářet virtuální počítače, Azure CLI vrátí výstup podobný tomuto:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Poznamenejte si hodnotu **publicIpAddress**. Tuto adresu použijete pro připojení k virtuálnímu počítači z Internetu v dalším kroku.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

V tomto příkazu nahraďte `<publicIpAddress>` s veřejnou IP adresu vašeho *myVm2* virtuálního počítače:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Chcete ověřit soukromou komunikaci mezi *myVm2* a *myVm1* virtuální počítače, zadejte tento příkaz:

```bash
ping myVm1 -c 4
```

Obdržíte čtyři odpovědi z *10.0.0.4*.

Ukončete relaci SSH s virtuálním počítačem *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete použít [odstranění skupiny az](/cli/azure/group#az_group_delete) k odebrání skupiny prostředků a všechny prostředky, které obsahuje:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. Připojení k jednomu virtuálnímu počítači z Internetu a Navázali jste soukromou komunikaci mezi dvěma virtuálními počítači. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md).

Azure umožňuje neomezenou soukromou komunikaci mezi virtuálními počítači. Ve výchozím nastavení Azure pouze umožní příchozí připojení ke vzdálené ploše pro virtuální počítače s Windows z Internetu. Další informace o konfiguraci různých typů síťové komunikace mezi virtuálním počítači, přejděte [filtrování síťového provozu](tutorial-filter-network-traffic.md) kurzu.