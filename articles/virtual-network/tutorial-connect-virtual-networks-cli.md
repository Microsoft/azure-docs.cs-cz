---
title: Připojit virtuální sítě pomocí virtuální sítě partnerský vztah - rozhraní příkazového řádku Azure | Microsoft Docs
description: V tomto článku zjistěte, jak připojit virtuální sítě pomocí virtuální sítě partnerský vztah, pomocí rozhraní příkazového řádku Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 29ab957e97c6aa57be6192e6ee4d86fe642ae95d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30321814"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Připojit virtuální sítě pomocí virtuální sítě pomocí rozhraní příkazového řádku Azure vytvoření partnerského vztahu.

Virtuální sítě můžete připojit k sobě navzájem s partnerský vztah virtuální sítě. Jakmile se kterými mají partnerský virtuální sítě, prostředky v obě virtuální sítě jsou komunikovat s mezi sebou, se stejnou latenci a šířky pásma, jako kdyby byly prostředky ve stejné virtuální síti. V tomto článku se dozvíte, jak:

* Vytvořte dvě virtuální sítě
* Připojení dvě virtuální sítě pomocí virtuální sítě partnerského vztahu
* Nasazení virtuálního počítače (VM) do každé virtuální sítě
* Komunikace mezi virtuálními počítači

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud si zvolíte instalaci a použití rozhraní příkazového řádku místně, v tomto článku vyžaduje, že používáte Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě, budete muset vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky, které jsou vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork1* s předponou adresy *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Vytvořit virtuální síť s názvem *myVirtualNetwork2* s předponou adresy *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Sdílené virtuální sítě

Partnerských vztahů se vytvoří mezi ID virtuální sítě, proto musíte nejprve získat ID každé virtuální síť s [az sítě vnet show](/cli/azure/network/vnet#az_network_vnet_show) a uložte ID v proměnné.

```azurecli-interactive
# Get the id for myVirtualNetwork1.
vNet1Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork1 \
  --query id --out tsv)

# Get the id for myVirtualNetwork2.
vNet2Id=$(az network vnet show \
  --resource-group myResourceGroup \
  --name myVirtualNetwork2 \
  --query id \
  --out tsv)
```

Vytvoření partnerského vztahu z *myVirtualNetwork1* k *myVirtualNetwork2* s [vnet az sítě partnerský vztah vytvořit](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Pokud `--allow-vnet-access` není zadán parametr, se naváže partnerský vztah, ale žádná komunikace můžete procházet skrz ho.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Ve výstupu vráceného po provedení předchozí příkaz, který uvidíte **peeringState** je *získaných*. Partnerského vztahu zůstane v aplikaci *získaných* stavu, dokud nevytvoříte partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. Vytvoření partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Ve výstupu vráceného po provedení předchozí příkaz, který uvidíte **peeringState** je *připojeno*. Azure také změnit stav partnerského vztahu *myVirtualNetwork1 myVirtualNetwork2* partnerského vztahu k *připojeno*. Potvrďte, že stav partnerského vztahu *myVirtualNetwork1 myVirtualNetwork2* partnerský vztah změnit tak, aby *připojeno* s [az sítě vnet partnerského vztahu zobrazit](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Prostředky v jedné virtuální sítě nemůže komunikovat s prostředky v jiné virtuální síti, dokud **peeringState** pro partnerské vztahy v obě virtuální sítě je *připojeno*. 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvoření virtuálního počítače v každé virtuální sítě, aby mohl komunikovat mezi nimi později.

### <a name="create-the-first-vm"></a>Vytvoření první virtuální počítač

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVm1* v *myVirtualNetwork1* virtuální sítě. Pokud se klíče SSH již neexistují v umístění klíče výchozí, vytvoří příkaz je. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. `--no-wait` Možnost vytvoří virtuální počítač na pozadí, abyste mohli pokračovat k dalšímu kroku.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork1 \
  --subnet Subnet1 \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Vytvořit druhý virtuální počítač

Vytvoření virtuálního počítače v *myVirtualNetwork2* virtuální sítě.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Virtuální počítač trvá několik minut pro vytvoření. Po vytvoření virtuálního počítače Azure CLI uvádí informace podobně jako v následujícím příkladu: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.1.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Poznamenejte si **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu do pozdějšího kroku.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Použijte následující příkaz pro vytvoření relace SSH s *Můjvp2* virtuálních počítačů. Nahraďte `<publicIpAddress>` s veřejnou IP adresu vašeho virtuálního počítače. V předchozím příkladu veřejná IP adresa je *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Příkaz ping virtuální počítač v *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Obdržíte čtyři odpovědi. 

Ukončení relace SSH k *Můjvp2* virtuálních počítačů. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak připojit ve stejné oblasti Azure, dvě sítě pomocí virtuální sítě partnerský vztah. Můžete také peer virtuální sítě v různých [podporované oblasti](virtual-network-manage-peering.md#cross-region) a v [různých předplatných Azure](create-peering-different-subscriptions.md#cli), stejně, jako je vytváření [hvězdicové sítě návrhů](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah. Další informace o partnerském vztahu virtuální sítě najdete v tématu [partnerského vztahu Přehled virtuálních sítí](virtual-network-peering-overview.md) a [spravovat virtuální sítě partnerských vztahů](virtual-network-manage-peering.md).

Můžete [svého počítače připojit k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) prostřednictvím sítě VPN a komunikovat s prostředky ve virtuální síti, nebo peered virtuální sítě. Znovu použitelné skripty k dokončení mnoho úloh, které jsou popsané v článcích virtuální sítě, naleznete v části [skript ukázky](cli-samples.md).