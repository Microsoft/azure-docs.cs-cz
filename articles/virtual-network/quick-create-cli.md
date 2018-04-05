---
title: Vytvoření virtuální sítě Azure - rozhraní příkazového řádku Azure | Microsoft Docs
description: Rychle se Naučte se vytvořit virtuální síť pomocí rozhraní příkazového řádku Azure. Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače, pro soukromě komunikaci mezi sebou a s Internetem.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: d07f06a1a70c859544c3b1ceb6146dc11e4d10aa
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Vytvoření virtuální sítě pomocí rozhraní příkazového řádku Azure

Virtuální síť umožňuje prostředky Azure, jako jsou virtuální počítače (VM), soukromě komunikaci mezi sebou a s Internetem. V tomto článku zjistěte, jak vytvořit virtuální síť. Po vytvoření virtuální sítě, můžete nasadit dva virtuální počítače do virtuální sítě. Pak připojit k virtuálnímu počítači jeden z Internetu a soukromě komunikovat s další virtuální počítač.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, v tomto článku vyžaduje, že používáte Azure CLI verze 2.0.28 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Než bude možné vytvořit virtuální síť, musíte vytvořit skupinu prostředků tak, aby obsahovala virtuální sítě. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří výchozí virtuální síť s názvem *myVirtualNetwork* s jednou podsítí s názvem *výchozí*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte dva virtuální počítače ve virtuální síti:

### <a name="create-the-first-vm"></a>Vytvoření první virtuální počítač

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Pokud se klíče SSH již neexistují v umístění klíče výchozí, vytvoří příkaz je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. `--no-wait` Možnost vytvoří virtuální počítač na pozadí, aby mohl pokračovat k dalšímu kroku. Následující příklad vytvoří virtuální počítač s názvem *myVm1*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Vytvořit druhý virtuální počítač

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače Azure CLI vrátí výstup podobně jako v následujícím příkladu: 

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

Poznamenejte si **publicIpAddress**. Tato adresa se používá pro připojení k virtuálnímu počítači z Internetu v dalším kroku.

## <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z Internetu

Nahraďte `<publicIpAddress>` s veřejnou IP adresu vašeho *Můjvp2* virtuálních počítačů v příkazu způsobem a potom zadejte následující příkaz:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>Soukromě komunikaci mezi virtuálními počítači

Potvrďte soukromé komunikace mezi *Můjvp2* a *myVm1* virtuální počítače, zadejte následující příkaz:

```bash
ping myVm1 -c 4
```

Obdržíte čtyři odpovědi od *10.0.0.4*.

Ukončení relace SSH s *Můjvp2* virtuálních počítačů.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, můžete použít [odstranění skupiny az](/cli/azure/group#az_group_delete) odebrat skupinu prostředků a všechny prostředky, které obsahuje:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili výchozí virtuální sítě a dva virtuální počítače. Připojený k jeden virtuální počítač z Internetu a soukromě přenášená mezi virtuálního počítače a jiným virtuálním Počítačem. Další informace o nastavení virtuální sítě najdete v tématu [spravovat virtuální sítě](manage-virtual-network.md). 

Ve výchozím nastavení Azure umožňuje neomezený privátní komunikaci mezi virtuálními počítači, ale umožňuje pouze příchozí relace SSH pro virtuální počítače s Linuxem z Internetu. Postup povolení nebo zakázání různé typy síťové komunikace do a z virtuálních počítačů, přechodu na v dalším kurzu.

> [!div class="nextstepaction"]
> [Filtrování provozu sítě přenosů](tutorial-filter-network-traffic-cli.md)
