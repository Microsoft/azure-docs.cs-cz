---
title: Otevřete porty, které se virtuální počítač s Linuxem pomocí Azure CLI 1.0 | Microsoft Docs
description: Zjistěte, jak otevřít port / create koncového bodu virtuálním počítačům s Linuxem pomocí modelu nasazení Azure resource manager a Azure CLI 1.0
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: e94b2ee3a14e9046703b66c00e3ba1a305dd3a7d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Otevření portů a koncové body pro virtuální počítač s Linuxem v Azure pomocí Azure CLI 1.0
Otevření portu nebo vytvořte koncového bodu, virtuální počítač (VM) v Azure vytvořením filtr sítě na podsíť nebo rozhraní sítě virtuálních počítačů. Tyto filtry, které řídí příchozí a odchozí přenosy, můžete umístit na skupinu zabezpečení sítě, který je připojen k prostředku, který přijímá provoz. Použijeme Běžným příkladem webové přenosy na portu 80. Tento článek ukazuje, jak otevřít port k virtuálnímu počítači pomocí Azure CLI 1.0.


## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](nsg-quickstart.md) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků


## <a name="quick-commands"></a>Rychlé příkazy
Chcete-li vytvořit skupinu zabezpečení sítě a pravidel, je nutné [Azure CLI 1.0](../../cli-install-nodejs.md) nainstalovaná a pomocí režimu Resource Manager:

```azurecli
azure config mode arm
```

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Názvy parametrů příklad zahrnuté *myResourceGroup*, *myNetworkSecurityGroup*, a *myVnet*.

Vytvořte vaši skupinu zabezpečení sítě, zadáte vlastní názvy a umístění správně. Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* v *eastus* umístění:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Přidáte pravidlo povolit provoz protokolu HTTP na svůj webový server (nebo upravit vlastní scénáře, jako je připojení SSH přístupu nebo databáze). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRule* umožňující komunikaci TCP na portu 80:

```azurecli
azure network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow
```

Přidružte skupinu zabezpečení sítě Virtuálního počítače síťové rozhraní (NIC). Následující příklad přiřadí stávající síťové karty s názvem *myNic* ke skupině zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --name myNic
```

Alternativně můžete přidružit vaše skupina zabezpečení sítě s podsítí virtuální sítě a nikoli pouze k síťové rozhraní na jeden virtuální počítač. Následující příklad přiřadí existující podsíť s názvem *mySubnet* v *myVnet* virtuální síť s skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
azure network vnet subnet set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy umožňují zprovoznění s provoz do virtuálního počítače. Skupiny zabezpečení sítě zadejte mnoho funkcí a členitosti pro řízení přístupu k prostředkům. Další informace o [vytvoření skupiny zabezpečení sítě a seznamu ACL pravidla zde](../../virtual-network/tutorial-filter-network-traffic-cli.md).

Skupiny zabezpečení sítě a pravidla seznamu ACL můžete definovat v rámci šablon Azure Resource Manager. Další informace o [vytvoření skupin zabezpečení sítě pomocí šablony](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Pokud budete muset použít předávání portů pro mapování jedinečný externí port pro interní port na vašem virtuálním počítači, použijte nástroj pro vyrovnávání zatížení a pravidla překladu adres (NAT). Můžete například chtít vystavit externě TCP port 8080 a mít data na port TCP 80 na virtuálním počítači. Dozvíte se o [zařízení na Vyrovnávání zatížení internetového](../../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili jednoduché pravidlo umožňující přenos HTTP. Můžete najít informace o vytváření podrobnější prostředí v těchto článcích:

* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
* [Co je skupina zabezpečení sítě (NSG)?](../../virtual-network/virtual-networks-nsg.md)
* [Přehled Azure Resource Manageru pro vyrovnávání zatížení](../../load-balancer/load-balancer-arm.md)

