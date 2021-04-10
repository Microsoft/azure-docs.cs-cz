---
title: Propojení virtuálních sítí s partnerským vztahem virtuální sítě – Azure CLI
description: V tomto článku se dozvíte, jak propojit virtuální sítě s využitím partnerského vztahu virtuálních sítí pomocí Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: df1b90a638052de7b56854060badfb8e3483d4c2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065425"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-the-azure-cli"></a>Propojení virtuálních sítí s využitím partnerského vztahu virtuálních sítí pomocí Azure CLI

Virtuální sítě můžete mezi sebou propojit s využitím partnerského vztahu virtuálních sítí. Po vytvoření partnerského vztahu virtuálních sítí budou moct prostředky v obou virtuálních sítích komunikovat mezi sebou se stejnou latencí a šířkou pásma, jako kdyby byly ve stejné virtuální síti. V tomto článku získáte informace o těchto tématech:

* Vytvoření dvou virtuálních sítí
* Propojení dvou virtuálních sítí s využitím partnerského vztahu virtuálních sítí
* Nasazení virtuálního počítače do obou virtuálních sítí
* Komunikace mezi virtuálními počítači

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-virtual-networks"></a>Vytvoření virtuálních sítí

Před vytvořením virtuální sítě je nutné vytvořit skupinu prostředků pro virtuální síť a všechny další prostředky vytvořené v tomto článku. Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

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

Partnerské vztahy se navázaly mezi identifikátory virtuální sítě, takže musíte nejdřív získat ID každé virtuální sítě pomocí [AZ Network VNet show](/cli/azure/network/vnet) a uložit ID v proměnné.

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

Vytvořte partnerský vztah z *myVirtualNetwork1* k *myVirtualNetwork2* pomocí [AZ Network VNet peering Create](/cli/azure/network/vnet/peering). Pokud `--allow-vnet-access` parametr není zadán, je vytvořen partnerský vztah, ale jeho prostřednictvím nemůže procházet žádná komunikace.

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --remote-vnet $vNet2Id \
  --allow-vnet-access
```

Ve výstupu vráceném po provedení předchozího příkazu se zobrazí zpráva, že **peeringState** je *inicializován*. Partnerský vztah zůstane v *inicializovaném* stavu, dokud nevytvoříte partnerský vztah z *myVirtualNetwork2* do *myVirtualNetwork1*. Vytvořte partnerský vztah z *myVirtualNetwork2* do *myVirtualNetwork1*. 

```azurecli-interactive
az network vnet peering create \
  --name myVirtualNetwork2-myVirtualNetwork1 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork2 \
  --remote-vnet $vNet1Id \
  --allow-vnet-access
```

Ve výstupu vráceném po provedení předchozího příkazu se zobrazí informace o tom, že je **PeeringState** *připojený*. Azure také změnil stav partnerského vztahu partnerského vztahu *myVirtualNetwork1-myVirtualNetwork2* na *připojeno*. Potvrďte, že stav partnerského vztahu pro partnerský vztah *myVirtualNetwork1-myVirtualNetwork2* se změnil na *připojeno* pomocí [AZ Network VNet peering show](/cli/azure/network/vnet/peering).

```azurecli-interactive
az network vnet peering show \
  --name myVirtualNetwork1-myVirtualNetwork2 \
  --resource-group myResourceGroup \
  --vnet-name myVirtualNetwork1 \
  --query peeringState
```

Prostředky v jedné virtuální síti nemůžou komunikovat s prostředky ve druhé virtuální síti, dokud nejsou *připojené* **peeringStatey** partnerských vztahů v obou virtuálních sítích. 

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte v obou virtuálních sítích virtuální počítač, abyste mezi nimi mohli v pozdějším kroku navázat komunikaci.

### <a name="create-the-first-vm"></a>Vytvoření prvního virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem *myVm1* ve virtuální síti *myVirtualNetwork1* . Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`. `--no-wait`Možnost vytvoří virtuální počítač na pozadí, takže můžete pokračovat k dalšímu kroku.

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

Vytvořte virtuální počítač ve virtuální síti *myVirtualNetwork2* .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork2 \
  --subnet Subnet1 \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu: 

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

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.

## <a name="communicate-between-vms"></a>Komunikace mezi virtuálními počítači

Pomocí následujícího příkazu vytvořte relaci SSH s virtuálním počítačem s *myVm2* . Nahraďte `<publicIpAddress>` veřejnou IP adresou vašeho virtuálního počítače. V předchozím příkladu je veřejná IP adresa *13.90.242.231*.

```bash
ssh <publicIpAddress>
```

Otestujte virtuální počítač v *myVirtualNetwork1*.

```bash
ping 10.0.0.4 -c 4
```

Dostanete čtyři odpovědi. 

Zavřete relaci SSH k virtuálnímu počítači *myVm2* . 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [AZ Group Delete](/cli/azure/group) .

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak propojit dvě sítě ve stejné oblasti Azure s partnerským vztahem virtuální sítě. Můžete vytvářet také partnerské vztahy virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region) a [různých předplatných Azure](create-peering-different-subscriptions.md#cli), stejně jako vytvářet [návrhy hvězdicovitých sítí](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) s partnerskými vztahy. Další informace o partnerských vztazích virtuálních sítí najdete v tématech [Přehled partnerských vztahů virtuálních sítí](virtual-network-peering-overview.md) a [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md).

[Vlastní počítač můžete připojit k virtuální síti](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pomocí sítě VPN a pracovat s prostředky ve virtuální síti nebo v partnerských virtuálních sítích. Aby bylo možné opakovaně použitelným skriptům dokončit mnoho úloh popsaných v článcích o virtuální síti, přečtěte si téma [ukázky skriptů](cli-samples.md).
