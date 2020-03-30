---
title: Vytvoření virtuální sítě – rychlý start – Azure CLI
titlesuffix: Azure Virtual Network
description: V tomto rychlém startu se naučíte vytvořit virtuální síť pomocí azure cli. Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou navzájem a s internetem.
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
ms.openlocfilehash: 2a4c63aeaa303692fa0f2d115a3df0d80cfab0b1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80235203"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Rychlý start: Vytvoření virtuální sítě pomocí Azure CLI

Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače (VM), komunikovat soukromě mezi sebou navzájem a s internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Potom se připojíte k virtuálním počítačům z Internetu a soukromě komunikujete prostřednictvím nové virtuální sítě.

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a použít Azure CLI místně místo, tento rychlý start vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte program `az --version`. Informace o instalaci nebo upgradu [najdete v tématu Instalace příkazového příkazového](/cli/azure/install-azure-cli) příkazu Konzumu Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Vytvoření skupiny prostředků a virtuální sítě

Před vytvořením virtuální sítě je třeba vytvořit skupinu prostředků pro hostování ve virtuální síti. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet). Tento příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí s názvem *výchozí*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Pokud klíče SSH ještě neexistují ve výchozím umístění klíče, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Pomocí možnosti `--no-wait` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku. Tento příklad vytvoří virtuální hod s názvem *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Since you used the `--no-wait` option in the previous step, you can go ahead and create the second VM named *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Výstupní zpráva Azure CLI

Vytvoření virtuálních počítačů trvá několik minut. Po Azure vytvoří virtuální počítače, Azure CLI vrátí výstup takto:

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

Poznamenejte si hodnotu **publicIpAddress**. Tuto adresu použijete k připojení k virtuálnímu virtuálnímu zařízení z internetu v dalším kroku.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

V tomto příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou virtuálního počítače *myVm2:*

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Chcete-li potvrdit soukromou komunikaci mezi virtuálními zařízeními *myVm2* a *myVm1,* zadejte tento příkaz:

```bash
ping myVm1 -c 4
```

Obdržíte čtyři odpovědi od *10.0.0.4*.

Ukončete relaci SSH s virtuálním počítačem *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, můžete pomocí [odstranění skupiny AZ](/cli/azure/group) odebrat skupinu prostředků a všechny prostředky, které má:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. Připojili jste se k jednomu virtuálnímu virtuálnímu zařízení z internetu a soukromě komunikovali mezi dvěma virtuálními aplikacemi. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md).

Azure umožňuje neomezenou privátní komunikaci mezi virtuálními počítači. Ve výchozím nastavení Azure umožňuje pouze příchozí připojení ke vzdálené ploše k virtuálním počítačům s Windows z internetu. Další informace o konfiguraci různých typů síťové komunikace virtuálních počítačů najdete v kurzu [Filtrování síťového provozu.](tutorial-filter-network-traffic.md)
