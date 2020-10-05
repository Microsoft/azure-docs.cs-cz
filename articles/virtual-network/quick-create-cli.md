---
title: Vytvoření virtuální sítě – rychlý Start – Azure CLI
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu se dozvíte, jak vytvořit virtuální síť pomocí Azure CLI. Virtuální síť umožňuje prostředkům Azure komunikovat mezi sebou a s internetem.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1feae201738a560c4cdb56f703c4af9a38af86d1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88056784"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Rychlý start: Vytvoření virtuální sítě pomocí Azure CLI

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Pak se k virtuálním počítačům připojíte z Internetu a soukromě komunikujte přes novou virtuální síť.
## <a name="prerequisites"></a>Předpoklady
Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Pokud chcete najít nainstalovanou verzi, spusťte příkaz `az --version` . Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .

## <a name="create-a-resource-group-and-a-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě

Než budete moct vytvořit virtuální síť, musíte vytvořit skupinu prostředků, která bude hostovat virtuální síť. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí s názvem *Default*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Pokud klíče SSH ve výchozím umístění klíče ještě neexistují, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Pomocí možnosti `--no-wait` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku. Tento příklad vytvoří virtuální počítač s názvem *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Vzhledem k tomu, že jste použili `--no-wait` možnost v předchozím kroku, můžete pokračovat a vytvořit druhý virtuální počítač s názvem *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Výstupní zpráva Azure CLI

Vytvoření virtuálních počítačů trvá několik minut. Po vytvoření virtuálních počítačů Azure CLI vrátí výstup podobný tomuto:

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se použije k připojení k virtuálnímu počítači z Internetu v dalším kroku.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

V tomto příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou vašeho virtuálního počítače s *myVm2* :

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Pokud chcete potvrdit privátní komunikaci mezi virtuálními počítači *myVm2* a *myVm1* , zadejte tento příkaz:

```bash
ping myVm1 -c 4
```

Dostanete čtyři odpovědi od *10.0.0.4*.

Ukončete relaci SSH s virtuálním počítačem *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [AZ Group Delete](/cli/azure/group) :

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. Připojili jste se k jednomu virtuálnímu počítači z Internetu a komunikovali soukromě mezi těmito dvěma virtuálními počítači.
Azure umožňuje neomezenou soukromou komunikaci mezi virtuálními počítači. Azure ve výchozím nastavení umožňuje pouze příchozí připojení vzdálené plochy k virtuálním počítačům s Windows z Internetu. V dalším článku se dozvíte víc o konfiguraci různých typů síťových komunikací virtuálních počítačů:
> [!div class="nextstepaction"]
> [Filtrování síťového provozu](tutorial-filter-network-traffic.md)
