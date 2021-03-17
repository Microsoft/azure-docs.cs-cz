---
title: Směrování síťového provozu – Azure CLI | Microsoft Docs
description: V tomto článku se dozvíte, jak směrovat síťový provoz pomocí směrovací tabulky pomocí Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 043d450a1b968174ad263579d39de06a296a98e4
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741480"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Směrování síťového provozu pomocí směrovací tabulky pomocí Azure CLI

Azure ve výchozím nastavení automaticky směruje provoz mezi všemi podsítěmi v rámci virtuální sítě. Můžete vytvořit vlastní trasy a přepsat tak výchozí směrování Azure. Možnost vytvářet vlastní trasy je užitečná například v případě, že chcete směrovat provoz mezi podsítěmi přes síťové virtuální zařízení. V tomto článku získáte informace o těchto tématech:

* Vytvoření směrovací tabulky
* Vytvoření trasy
* Vytvoření virtuální sítě s několika podsítěmi
* Přidružení směrovací tabulky k podsíti
* Vytvoření síťového virtuálního zařízení, které směruje provoz
* Nasazení virtuálních počítačů do různých podsítí
* Směrování provozu z jedné podsítě do jiné přes síťové virtuální zařízení

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.28 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-route-table"></a>Vytvoření směrovací tabulky

Než budete moct vytvořit směrovací tabulku, vytvořte skupinu prostředků pomocí metody [AZ Group Create](/cli/azure/group) pro všechny prostředky vytvořené v tomto článku. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

Vytvořte směrovací tabulku pomocí [AZ Network Route-Table Create](/cli/azure/network/route-table#az-network-route-table-create). Následující příklad vytvoří směrovací tabulku s názvem *myRouteTablePublic*. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Vytvoření trasy

Vytvořte trasu v tabulce směrování pomocí [AZ Network Route-Table Route Create](/cli/azure/network/route-table/route#az-network-route-table-route-create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Přidružení směrovací tabulky k podsíti

Předtím, než budete moci přidružit směrovací tabulku k podsíti, je nutné vytvořit virtuální síť a podsíť. Vytvořte virtuální síť s jednou podsítí pomocí [AZ Network VNet Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Vytvořte dvě další podsítě pomocí [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

Přidružte tabulku směrování *myRouteTablePublic* k *veřejné* podsíti pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>Vytvoření síťového virtuálního zařízení

Síťové virtuální zařízení je virtuální počítač, který provádí síťovou funkci, jako je směrování, brána firewall nebo optimalizace sítě WAN.

Vytvořte síťové virtuální zařízení v podsíti *DMZ* pomocí [AZ VM Create](/cli/azure/vm). Když vytvoříte virtuální počítač, Azure ve výchozím nastavení vytvoří a přiřadí veřejné IP adresy k virtuálnímu počítači. `--public-ip-address ""`Parametr dá službě Azure pokyn, aby nevytvořila a přiřadila veřejnou IP adresu virtuálnímu počítači, protože virtuální počítač nemusí být připojený k Internetu. Pokud ve výchozím umístění klíčů ještě neexistují klíče SSH, příkaz je vytvoří. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Nepokračujte k dalšímu kroku, dokud Azure nedokončí vytváření virtuálního počítače a vrátí výstup o virtuálním počítači. 

Aby síťové rozhraní mohlo směrovat síťový provoz, který se do něj odešle a který není určený pro jeho vlastní IP adresu, musí být pro síťové rozhraní povolené předávání IP. Povolte předávání IP pro síťové rozhraní pomocí [AZ Network nic Update](/cli/azure/network/nic).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Operační systém nebo aplikace spuštěná v rámci virtuálního počítače musí také být schopné směrovat síťový provoz. Povolte předávání IP v rámci operačního systému virtuálního počítače pomocí [AZ VM Extension set](/cli/azure/vm/extension):

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Provedení příkazu může trvat až minutu.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte ve virtuální síti dva virtuální počítače, abyste mohli ověřit, že provoz z *veřejné* podsítě je směrován do *privátní* podsítě prostřednictvím síťové virtuální zařízení v pozdějším kroku. 

Vytvořte virtuální počítač ve *veřejné* podsíti pomocí [AZ VM Create](/cli/azure/vm). `--no-wait`Parametr umožňuje službě Azure spustit příkaz na pozadí, takže můžete pokračovat k dalšímu příkazu. Pro zjednodušení tohoto článku se používá heslo. Klíče se obvykle používají v produkčních nasazeních. Pokud používáte klíče, musíte také nakonfigurovat předávání agenta SSH. Další informace najdete v dokumentaci ke klientovi SSH. `<replace-with-your-password>`V následujícím příkazu nahraďte heslem, které jste si zvolili.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Vytvořte virtuální počítač v *privátní* podsíti.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Vytvoření virtuálního počítače trvá několik minut. Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Poznamenejte si hodnotu **publicIpAddress**. Tato adresa se používá pro přístup k virtuálnímu počítači z Internetu v pozdějším kroku.

## <a name="route-traffic-through-an-nva"></a>Směrování provozu přes síťové virtuální zařízení

Pomocí následujícího příkazu vytvořte relaci SSH s virtuálním počítačem s *myVmPrivate* . Nahraďte *\<publicIpAddress>* veřejnou IP adresou vašeho virtuálního počítače. V předchozím příkladu je IP adresa *13.90.242.231*.

```bash
ssh azureuser@<publicIpAddress>
```

Po zobrazení výzvy k zadání hesla zadejte heslo, které jste vybrali v části [vytvořit virtuální počítače](#create-virtual-machines).

K instalaci trasy trasování na virtuálním počítači s *myVmPrivate* použijte následující příkaz:

```bash
sudo apt-get install traceroute
```

Pomocí následujícího příkazu otestujte směrování síťového provozu do virtuálního počítače *myVmPublic* z virtuálního počítače *myVmPrivate* .

```bash
traceroute myVmPublic
```

Odpověď bude podobná jako v následujícím příkladu:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Jak vidíte, provoz se směruje přímo z virtuálního počítače *myVmPrivate* do virtuálního počítače *myVmPublic*. Výchozí trasy Azure směrují provoz přímo mezi podsítěmi. 

Z virtuálního počítače *myVmPrivate* použijte následující příkaz pro SSH k virtuálnímu počítači s *myVmPublic* :

```bash
ssh azureuser@myVmPublic
```

K instalaci trasy trasování na virtuálním počítači s *myVmPublic* použijte následující příkaz:

```bash
sudo apt-get install traceroute
```

Pomocí následujícího příkazu otestujte směrování síťového provozu do virtuálního počítače *myVmPrivate* z virtuálního počítače *myVmPublic* .

```bash
traceroute myVmPrivate
```

Odpověď bude podobná jako v následujícím příkladu:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Jak vidíte, první segment směrování je 10.0.2.4, což je privátní IP adresa síťového virtuálního zařízení. Druhý segment směrování je 10.0.1.4, což je privátní IP adresa virtuálního počítače *myVmPrivate*. Trasa přidaná do směrovací tabulky *myRouteTablePublic* a přidružená k podsíti *Public* způsobila, že Azure směruje provoz přes síťové virtuální zařízení, a ne přímo do podsítě *Private*.

Zavřete relace SSH na virtuální počítače *myVmPublic* i *myVmPrivate* .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků a všechny prostředky, které obsahuje, pomocí [AZ Group Delete](/cli/azure/group) .

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili směrovací tabulku a přidružili ji k podsíti. Vytvořili jste jednoduché síťové virtuální zařízení, které směrovalo provoz z veřejné podsítě do privátní podsítě. Z webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) můžete nasadit různá předem nakonfigurovaná síťová virtuální zařízení, která provádí síťové funkce, jako je brána firewall a optimalizace sítě WAN. Další informace o směrování najdete v tématech [Přehled směrování](virtual-networks-udr-overview.md) a [Správa směrovací tabulky](manage-route-table.md).

Přestože v rámci virtuální sítě můžete nasadit řadu prostředků Azure, prostředky některých služeb Azure PaaS do virtuální sítě nasadit nejde. Přesto můžete omezit přístup k prostředkům některých služeb Azure PaaS pouze pro provoz z podsítě virtuální sítě. Další informace najdete v tématu [omezení síťového přístupu k prostředkům PaaS](tutorial-restrict-network-access-to-resources-cli.md).
