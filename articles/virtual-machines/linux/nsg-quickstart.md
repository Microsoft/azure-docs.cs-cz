---
title: Otevření portů k virtuálnímu počítači pomocí azure cli
description: Naučte se, jak otevřít port / vytvořit koncový bod pro virtuální počítač pomocí Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: c29fb075fc2d8b512070d7a6cf3fef949def5894
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066631"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Otevření portů a koncových bodů virtuálnímu počítači pomocí azure cli

Otevřete port nebo vytvoříte koncový bod pro virtuální počítač (VM) v Azure vytvořením síťového filtru v podsíti nebo síťovém rozhraní virtuálního počítače. Tyto filtry, které řídí příchozí i odchozí provoz, umístíte do skupiny zabezpečení sítě připojené k prostředku, který přijímá přenosy. Použijeme běžný příklad webového provozu na portu 80. Tento článek ukazuje, jak otevřít port pro virtuální počítač s Azure CLI. 


Chcete-li vytvořit skupinu zabezpečení sítě a pravidla, potřebujete nejnovější [azure cli](/cli/azure/install-az-cli2) nainstalované a přihlášené k účtu Azure pomocí [přihlášení az](/cli/azure/reference-index).

V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Mezi příklady názvů parametrů patří *myResourceGroup*, *myNetworkSecurityGroup*a *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Rychlé otevření portu pro virtuální hod
Pokud potřebujete rychle otevřít port pro virtuální počítače v scénáři pro vývoj a testování, můžete použít příkaz [az vm open-port.](/cli/azure/vm) Tento příkaz vytvoří skupinu zabezpečení sítě, přidá pravidlo a použije ho na virtuální ho disponovatelnebo podsíť. Následující příklad otevře port *80* na virtuálním počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Chcete-li získat větší kontrolu nad pravidly, jako je například definování rozsahu zdrojových adres IP, pokračujte dalšími kroky v tomto článku.


## <a name="create-a-network-security-group-and-rules"></a>Vytvoření skupiny zabezpečení sítě a pravidel
Vytvořte skupinu zabezpečení sítě pomocí vytvoření [sítě az nsg](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* v umístění *eastus:*

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Přidejte pravidlo s [az network nsg rule create,](/cli/azure/network/nsg/rule) které umožní přenos http na váš webový server (nebo upravte vlastní scénář, například přístup SSH nebo připojení k databázi). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRule,* které povoluje přenos protokolu TCP na portu 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Použití skupiny zabezpečení sítě pro virtuální ms
Přidružte skupinu zabezpečení sítě k síťovému rozhraní virtuálního počítače (NIC) k [aktualizaci az network nic](/cli/azure/network/nic). Následující příklad přidruží existující síťovou síťovou síť ovou *síť* s názvem *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Případně můžete skupinu zabezpečení sítě přidružit k podsíti virtuální sítě k [aktualizaci podsítě sítě AZ network,](/cli/azure/network/vnet/subnet) nikoli pouze k síťovému rozhraní na jednom virtuálním počítači. Následující příklad přidruží existující podsíť s názvem *mySubnet* ve virtuální síti *myVnet* se skupinou zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy zde umožňují vstát a spustit s provozem proudícím do virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a rozlišovací schopnost pro řízení přístupu k prostředkům. Další informace o [vytváření skupiny zabezpečení sítě a pravidel seznamu Řízení přístupu](tutorial-virtual-network.md#secure-network-traffic)naleznete zde .

Pro vysoce dostupné webové aplikace byste měli umístit virtuální počítače za Azure Balancer. Správce načítání distribuuje provoz do virtuálních pozemních disponujících pomocí skupiny zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [Jak vyrovnat vyrovnávání zatížení virtuálních počítačů Linuxu v Azure k vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další kroky
V tomto příkladu jste vytvořili jednoduché pravidlo umožňující přenos http. Informace o vytváření podrobnějších prostředí naleznete v následujících článcích:

* [Přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md)
* [Co je skupina zabezpečení sítě (NSG)?](../../virtual-network/security-overview.md)
