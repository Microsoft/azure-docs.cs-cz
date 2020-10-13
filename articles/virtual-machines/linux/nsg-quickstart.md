---
title: Otevření portů k virtuálnímu počítači pomocí Azure CLI
description: Přečtěte si, jak otevřít port/vytvořit koncový bod pro virtuální počítač pomocí Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a96c0f7c6fb767b96be273a615149143043e8bc1
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975123"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Otevření portů a koncových bodů pro virtuální počítač pomocí Azure CLI

Otevřete port nebo vytvořte koncový bod s virtuálním počítačem v Azure vytvořením síťového filtru v podsíti nebo síťovém rozhraní virtuálního počítače. Tyto filtry umístíte, což řídí příchozí i odchozí provoz, ve skupině zabezpečení sítě připojené k prostředku, který přijímá provoz. Pojďme použít běžný příklad webového provozu na portu 80. V tomto článku se dozvíte, jak otevřít port k virtuálnímu počítači pomocí Azure CLI. 


Pokud chcete vytvořit skupinu zabezpečení sítě a pravidla, která potřebujete, nainstalujte nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlaste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů jsou *myResourceGroup*, *myNetworkSecurityGroup*a *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Rychlé otevření portu pro virtuální počítač
Pokud potřebujete rychle otevřít port pro virtuální počítač ve scénáři pro vývoj/testování, můžete použít příkaz [AZ VM Open-port](/cli/azure/vm) . Tento příkaz vytvoří skupinu zabezpečení sítě, přidá pravidlo a použije ho pro virtuální počítač nebo podsíť. Následující příklad otevře port *80* na virtuálním počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Pro lepší kontrolu pravidel, jako je například definování rozsahu zdrojových IP adres, pokračujte dalšími kroky v tomto článku.


## <a name="create-a-network-security-group-and-rules"></a>Vytvoření skupiny zabezpečení sítě a pravidel
Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* v umístění *eastus* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Přidejte pravidlo pomocí [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule) , aby se POVOLIL přenos HTTP na váš server (nebo upravte vlastní situaci, třeba přístup SSH nebo připojení k databázi). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRule* , které povoluje přenosy TCP na portu 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Použít skupinu zabezpečení sítě pro virtuální počítač
Přidružte skupinu zabezpečení sítě k síťovému rozhraní (NIC) vašeho virtuálního počítače pomocí [AZ Network nic Update](/cli/azure/network/nic). Následující příklad přidruží stávající síťové rozhraní s názvem *myNic* ke skupině zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Případně můžete skupinu zabezpečení sítě přidružit k podsíti virtuální sítě pomocí [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet) místo pouze síťového rozhraní na jednom virtuálním počítači. Následující příklad přidruží existující podsíť s názvem *mySubnet* ve virtuální síti *MyVnet* se skupinou zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy, které vám pomůžou začít pracovat s přenosem dat do virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a členitosti pro řízení přístupu k vašim prostředkům. Tady si můžete přečíst další informace o [vytváření skupin zabezpečení sítě a pravidel seznamu ACL](tutorial-virtual-network.md#secure-network-traffic).

Pro webové aplikace s vysokou dostupností byste měli své virtuální počítače umístit za Azure Load Balancer. Nástroj pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů pomocí skupiny zabezpečení sítě, která poskytuje filtrování přenosů. Další informace najdete v tématu [Postup při vyrovnávání zatížení virtuálních počítačů se systémem Linux v Azure za účelem vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další kroky
V tomto příkladu jste vytvořili jednoduché pravidlo pro povolení přenosů HTTP. Informace o vytváření podrobnějších prostředí najdete v následujících článcích:

* [Přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Co je skupina zabezpečení sítě (NSG)?](../../virtual-network/network-security-groups-overview.md)