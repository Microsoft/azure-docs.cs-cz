---
title: Otevření portů k virtuálnímu počítači s Linuxem pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak otevřít port / vytvořit koncový bod virtuálního počítače s Linuxem pomocí modelu nasazení Azure resource manager a Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: dd254f0640e17bdc055171dde69b04103f389354
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55659826"
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Otevření portů a koncových bodů pro virtuální počítač s Linuxem pomocí Azure CLI

Otevření portu nebo vytvoření koncového bodu, virtuálního počítače (VM) v Azure vytvořte filtr sítě pro podsíť nebo síťové rozhraní virtuálního počítače. Tyto filtry, které řídí příchozí a odchozí přenosy dat, umístíte na skupinu zabezpečení sítě, který je připojen k prostředku, který přijímá provoz. Použijeme běžným Příkladem webového provozu na portu 80. Tento článek popisuje postup otevření portů k virtuálnímu počítači pomocí rozhraní příkazového řádku Azure. 


Chcete-li vytvořit skupinu zabezpečení sítě a pravidel, je třeba nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte ukázkové názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad *myResourceGroup*, *myNetworkSecurityGroup*, a *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Rychle otevření portu pro virtuální počítač
Pokud potřebujete rychle otevření portu pro virtuální počítač ve scénáři pro vývoj/testování, můžete použít [az vm open-port](/cli/azure/vm#az_vm_open_port) příkazu. Tento příkaz vytvoří skupinu zabezpečení sítě, přidá pravidlo a použije ho k virtuálnímu počítači nebo podsítě. Následující příklad otevře port *80* na virtuálním počítači s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Pro větší kontrolu nad pravidla, například definování zdrojového rozsahu IP adres pokračujte další kroky v tomto článku.


## <a name="create-a-network-security-group-and-rules"></a>Vytvoření skupiny zabezpečení sítě a pravidel
Vytvořit skupinu zabezpečení sítě s [az network nsg vytvořit](/cli/azure/network/nsg). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* v *eastus* umístění:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Přidat pravidlo s [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) povolit provoz protokolu HTTP na svůj webový server (nebo upravit vlastní scénáře, jako je například přístup nebo databáze připojení SSH). Následující příklad vytvoří pravidlo *myNetworkSecurityGroupRule* umožňující provoz TCP na portu 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Použít skupinu zabezpečení sítě k virtuálnímu počítači
Přidružení skupiny zabezpečení sítě virtuálního počítače a síťové rozhraní (NIC) s [az network nic update](/cli/azure/network/nic#az_network_nic_update). Následující příklad přiřadí existující síťovou kartu s názvem *myNic* se skupinou zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Alternativně můžete přidružit skupiny zabezpečení sítě k podsíti virtuální sítě s [az network vnet podsíť aktualizace](/cli/azure/network/vnet/subnet) , nikoli pouze k síťovému rozhraní na jeden virtuální počítač. Následující příklad přiřadí existující podsíť s názvem *mySubnet* v *myVnet* virtuální sítě se skupinou zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy umožní začít pracovat s přenosy směřující do virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a členitost řízení přístupu k vašim prostředkům. Další informace o [vytvoření skupiny zabezpečení sítě a seznamu ACL pravidla zde](tutorial-virtual-network.md#secure-network-traffic).

U vysoce dostupných webových aplikací by měl umístěte své virtuální počítače za nástrojem Azure Load Balancer. Nástroje pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů pomocí skupiny zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [zatížení vyrovnávat virtuální počítače s Linuxem v Azure za účelem vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili jednoduché pravidlo pro povolení provozu HTTP. Můžete najít informace o vytváření podrobnější prostředí v následujících článcích:

* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
* [Co je skupina zabezpečení sítě (NSG)?](../../virtual-network/security-overview.md)
