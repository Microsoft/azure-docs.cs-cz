---
title: Připojení virtuálních sítí pomocí partnerského vztahu virtuální sítě – rozhraní příkazového příkazu Azure
description: V tomto článku se dozvíte, jak připojit virtuální sítě s partnerským vztahem virtuální sítě pomocí azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: aa2d75173b14e768a207336b54b3dc10a8c3ea5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235165"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Propojení virtuálních sítí s partnerským vztahem virtuální sítě pomocí azure cli

Virtuální sítě můžete mezi sebou propojit s využitím partnerského vztahu virtuálních sítí. Po vytvoření partnerského vztahu virtuálních sítí budou moct prostředky v obou virtuálních sítích komunikovat mezi sebou se stejnou latencí a šířkou pásma, jako kdyby byly ve stejné virtuální síti. V tomto článku získáte informace o těchto tématech:

* Vytvoření dvou virtuálních sítí
* Propojení dvou virtuálních sítí s využitím partnerského vztahu virtuálních sítí
* Nasazení virtuálního počítače do obou virtuálních sítí
* Komunikace mezi virtuálními počítači

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě je třeba vytvořit skupinu prostředků pro virtuální síť a všechny ostatní prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Vytvořte virtuální síť pomocí příkazu [az network vnet create](/cli/azure/network/vnet). Následující příklad vytvoří virtuální síť s názvem *myVirtualNetwork1* s předponou adresy *10.0.0.0/16*.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.0.0.0/24
```

Vytvořte virtuální síť s názvem *myVirtualNetwork2* s předponou adresy *10.1.0.0/16*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --address-prefixes 10.1.0.0/16 \
  --subnet-name Subnet1 \
  --subnet-prefix 10.1.0.0/24
```

## <a name="peer-virtual-networks"></a>Vytvoření partnerského vztahu virtuálních sítí

Partnerských společností jsou vytvořeny mezi ID virtuální sítě, takže musíte nejprve získat ID každé virtuální sítě s [az sítě virtuální sítě zobrazit](/cli/azure/network/vnet) a uložit ID v proměnné.

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

Vytvořte partnerský vztah z *myVirtualNetwork1* na *myVirtualNetwork2* s [vytvořením partnerského vztahu sítě az](/cli/azure/network/vnet/peering). Pokud `--allow-vnet-access` parametr není zadán, je vytvořen partnerský vztah, ale žádná komunikace může protékat.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Ve výstupu vráceném po provedení předchozího příkazu uvidíte, že **peeringState** je *Initiated*. Partnerský vztah zůstane ve stavu *Zahájeno,* dokud nevytvoříte partnerský vztah z *myVirtualNetwork2* na *myVirtualNetwork1*. Vytvořte partnerský vztah z *myVirtualNetwork2* do *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Ve výstupu vráceném po provedení předchozího příkazu uvidíte, že **peeringState** je *Připojeno*. Azure také změnil stav partnerského vztahu partnerského vztahu partnerského vztahu *myVirtualNetwork1-myVirtualNetwork2* na *Connected*. Zkontrolujte, zda se stav partnerského vztahu pro partnerský vztah *myVirtualNetwork1-myVirtualNetwork2* změnil na *Připojeno* s [pořadem partnerského vztahu sítě AZ](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Prostředky v jedné virtuální síti nemohou komunikovat s prostředky v jiné virtuální **síti,** dokud není partnerský stav pro partnerské partnery v obou virtuálních sítích *připojen*. 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v obou virtuálních sítích virtuální počítač, abyste mezi nimi mohli v pozdějším kroku navázat komunikaci.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem *myVm1* ve virtuální síti *myVirtualNetwork1.* Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. Tato `--no-wait` možnost vytvoří virtuální hovirtuální ho na pozadí, takže můžete pokračovat k dalšímu kroku.

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

Vytvořte virtuální počítač ve virtuální síti *myVirtualNetwork2.*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače azure cli zobrazí informace podobné v následujícím příkladu: 

```output
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

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu virtuálnímu mněmu z internetu v pozdějším kroku.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Pomocí následujícího příkazu vytvořte relaci SSH s virtuálním virtuálním *mem myVm2.* Nahraďte `<publicIpAddress>` veřejnou IP adresou virtuálního počítače. V předchozím příkladu je veřejná IP adresa *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Příkaz ping na virtuální počítač v *programu myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Obdržíte čtyři odpovědi. 

Zavřete relaci SSH na virtuální m *myVm2.* 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, použijte [az skupiny odstranit](/cli/azure/group) odebrat skupinu prostředků a všechny prostředky, které obsahuje.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak připojit dvě sítě ve stejné oblasti Azure, s partnerským vztahem virtuální sítě. Můžete vytvářet také partnerské vztahy virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region) a [různých předplatných Azure](create-peering-different-subscriptions.md#cli), stejně jako vytvářet [návrhy hvězdicovitých sítí](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) s partnerskými vztahy. Další informace o partnerských vztazích virtuálních sítí najdete v tématech [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md).

Vlastní [počítač](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) můžete připojit k virtuální síti prostřednictvím sítě VPN a pracovat s prostředky ve virtuální síti nebo ve virtuálních sítích. Opakovaně použitelné skripty k dokončení mnoha úkolů zahrnutých v článcích virtuální sítě naleznete v [tématu ukázky skriptů](cli-samples.md).
