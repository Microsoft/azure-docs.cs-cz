---
title: Propojení virtuálních sítí s využitím partnerského vztahu virtuální sítě – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak k propojení virtuálních sítí s využitím partnerského vztahu virtuálních sítí pomocí Azure CLI.
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
ms.openlocfilehash: 0f98f815c0417d1ee8ad5708589b5402a033f83a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989530"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Propojení virtuálních sítí pomocí partnerského vztahu virtuálních sítí pomocí rozhraní příkazového řádku Azure

Virtuální sítě můžete mezi sebou propojit s využitím partnerského vztahu virtuálních sítí. Po vytvoření partnerského vztahu virtuálních sítí budou moct prostředky v obou virtuálních sítích komunikovat mezi sebou se stejnou latencí a šířkou pásma, jako kdyby byly ve stejné virtuální síti. V tomto článku získáte informace o těchto tématech:

* Vytvoření dvou virtuálních sítí
* Propojení dvou virtuálních sítí s využitím partnerského vztahu virtuálních sítí
* Nasazení virtuálního počítače do obou virtuálních sítí
* Komunikace mezi virtuálními počítači

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě, je nutné vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

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

Vytvoření virtuální sítě s názvem *myVirtualNetwork2* s předponou adresy *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Vytvoření partnerského vztahu virtuálních sítí

Partnerské vztahy se vytvoří mezi ID virtuální sítě, proto musíte nejprve získat ID každé virtuální síti s [az network vnet show](/cli/azure/network/vnet#az_network_vnet_show) a Identifikátoru uložit jako proměnnou.

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

Vytvoření partnerského vztahu z *myVirtualNetwork1* k *myVirtualNetwork2* s [vytvořit partnerský vztah virtuální sítě sítě az](/cli/azure/network/vnet/peering#az_network_vnet_peering_create). Pokud `--allow-vnet-access` parametr není zadán, se naváže partnerský vztah, ale žádná komunikace probíhá přes ho.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet-id $vNet2Id \
  --allow-vnet-access
```

Ve výstupu vráceného po spuštění předchozího příkazu, uvidíte, že **peeringState** je *iniciováno*. Vytvoření partnerského vztahu zůstává ve *iniciováno* stavu, dokud vytvoříte partnerské připojení z *myVirtualNetwork2* k *myVirtualNetwork1*. Vytvoření partnerského vztahu z *myVirtualNetwork2* k *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet-id $vNet1Id \
  --allow-vnet-access
```

Ve výstupu vráceného po spuštění předchozího příkazu, uvidíte, že **peeringState** je *připojeno*. Azure také změnilo stav partnerského vztahu *myVirtualNetwork1-myVirtualNetwork2* partnerského vztahu pro *připojeno*. Ujistěte se, že stav partnerského vztahu pro *myVirtualNetwork1-myVirtualNetwork2* partnerský vztah změněn na *připojeno* s [az sítě zobrazit partnerského vztahu virtuálních sítí](/cli/azure/network/vnet/peering#az_network_vnet_peering_show).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Prostředky v jedné virtuální síti nemůžou komunikovat s prostředky v jiné virtuální síti, dokud **peeringState** pro partnerské vztahy v obou virtuálních sítích je *připojeno*. 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v obou virtuálních sítích virtuální počítač, abyste mezi nimi mohli v pozdějším kroku navázat komunikaci.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *myVm1* v *myVirtualNetwork1* virtuální sítě. Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. `--no-wait` Možnost se virtuální počítač vytvoří na pozadí, takže můžete pokračovat k dalšímu kroku.

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

### <a name="create-the-second-vm"></a>Vytvoření druhého virtuálního počítače

Vytvářet virtuální počítače *myVirtualNetwork2* virtuální sítě.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače Azure CLI zobrazí podobné informace jako v následujícím příkladu: 

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

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Pomocí následujícího příkazu vytvořte relaci SSH s *myVm2* virtuálního počítače. Nahraďte `<publicIpAddress>` veřejnou IP adresou vašeho virtuálního počítače. V předchozím příkladu, veřejná IP adresa je *13.90.242.231*.

```bash 
ssh <publicIpAddress>
```

Příkaz ping virtuálnímu počítači v *myVirtualNetwork1*.

```bash 
ping 10.0.0.4 -c 4
```

Obdržíte čtyři odpovědi. 

Ukončete relaci SSH k *myVm2* virtuálního počítače. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, použijte [odstranění skupiny az](/cli/azure/group#az_group_delete) k odebrání skupiny prostředků a všech prostředků, které obsahuje.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak propojit dvě sítě ve stejné oblasti Azure s využitím partnerského vztahu virtuální sítě. Můžete vytvářet také partnerské vztahy virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region) a [různých předplatných Azure](create-peering-different-subscriptions.md#cli), stejně jako vytvářet [návrhy hvězdicovitých sítí](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerskými vztahy. Další informace o partnerských vztazích virtuálních sítí najdete v tématech [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md).

Je možné [připojení vlastního počítače k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) prostřednictvím sítě VPN a práci s prostředky ve virtuální síti nebo v partnerských virtuálních sítích. Opakovaně použitelné skriptů k provedení mnoha z úlohy najdete v článcích virtuální sítě, naleznete v tématu [ukázkové skripty](cli-samples.md).