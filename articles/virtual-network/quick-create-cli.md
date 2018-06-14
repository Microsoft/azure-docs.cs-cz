---
title: Vytvoření virtuální sítě – Rychlý start – Azure CLI | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak vytvořit virtuální síť pomocí webu Azure Portal. Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: bb45b2b4ecd89187e94066bc81782174738fe3a9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842284"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Rychlý start: Vytvoření virtuální sítě pomocí Azure CLI

Virtuální síť umožňuje prostředkům Azure, jako jsou virtuální počítače, komunikovat soukromě mezi sebou a s internetem. V tomto rychlém startu zjistíte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě do ní nasadíte dva virtuální počítače. Pak se k jednomu virtuálnímu počítači připojíte z internetu a navážete soukromou komunikaci s druhým virtuálním počítačem.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Než budete moct vytvořit virtuální síť, musíte vytvořit skupinu prostředků, která bude virtuální síť obsahovat. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří výchozí virtuální síť *myVirtualNetwork* s jednou podsítí *default*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače:

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Pomocí možnosti `--no-wait` se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku. Následující příklad vytvoří virtuální počítač *myVm1*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače vrátí Azure CLI podobný výstup jako v následujícím příkladu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se v dalším kroku použije k připojení k virtuálnímu počítači z internetu.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

V následujícím příkazu nahraďte `<publicIpAddress>` veřejnou IP adresou virtuálního počítače *myVm2* a pak ho zadejte:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Pokud chcete ověřit soukromou komunikaci mezi virtuálními počítači *myVm2* a *myVm1*. zadejte následující příkaz:

```bash
ping myVm1 -c 4
```

Obdržíte čtyři odpovědi z adresy *10.0.0.4*.

Ukončete relaci SSH s virtuálním počítačem *myVm2*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít příkaz [az group delete](/cli/azure/group#az_group_delete):

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili výchozí virtuální síť a dva virtuální počítače. K jednomu virtuálnímu počítači jste se připojili z internetu a navázali jste soukromou komunikaci mezi tímto a druhým virtuálním počítačem. Další informace o nastavení virtuální sítě najdete v tématu [Správa virtuální sítě](manage-virtual-network.md). 

Ve výchozím nastavení Azure umožňuje neomezenou soukromou komunikaci mezi virtuálními počítači, ale u virtuálních počítačů s Windows povoluje pouze příchozí připojení ke vzdálené ploše z internetu. Informace o povolení nebo zakázání různých typů síťové komunikace do a z virtuálních počítačů najdete v tématu [Filtrování síťového provozu](tutorial-filter-network-traffic.md).
