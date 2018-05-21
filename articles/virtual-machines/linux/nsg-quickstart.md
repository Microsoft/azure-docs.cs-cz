---
title: Otevřete porty, které se virtuální počítač s Linuxem pomocí Azure CLI 2.0 | Microsoft Docs
description: Zjistěte, jak otevřít port / create koncového bodu virtuálním počítačům s Linuxem pomocí modelu nasazení Azure resource manager a Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 16493d7ecdec0ec1464820be7668dfa19ec1b13c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Otevřete porty a koncové body pro virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure
Otevření portu nebo vytvořte koncového bodu, virtuální počítač (VM) v Azure vytvořením filtr sítě na podsíť nebo rozhraní sítě virtuálních počítačů. Tyto filtry, které řídí příchozí a odchozí přenosy, můžete umístit na skupinu zabezpečení sítě, který je připojen k prostředku, který přijímá provoz. Použijeme Běžným příkladem webové přenosy na portu 80. Tento článek ukazuje, jak otevřít port pro virtuální počítač s Azure CLI 2.0. K provedení těchto kroků můžete také využít [Azure CLI 1.0](nsg-quickstart-nodejs.md).

Chcete-li vytvořit skupinu zabezpečení sítě a pravidel, je třeba nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad *myResourceGroup*, *myNetworkSecurityGroup*, a *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Rychle otevření portu pro virtuální počítač
Pokud budete potřebovat rychle otevření portu pro virtuální počítač ve scénáři vývoje/testování, můžete použít [az virtuálních počítačů open-port](/cli/azure/vm#az_vm_open_port) příkaz. Tento příkaz vytvoří skupinu zabezpečení sítě, přidá pravidlo a použije ji na virtuální počítač nebo podsítě. Následující příklad otevře port *80* ve virtuálním počítači s názvem *Můjvp* ve skupině prostředků s názvem *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Pro větší kontrolu nad pravidla, jako je například definování rozsah adres IP zdroje pokračujte další kroky v tomto článku.


## <a name="create-a-network-security-group-and-rules"></a>Vytvořte skupinu zabezpečení sítě a pravidla
Vytvořit skupinu zabezpečení sítě s [vytvořit az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create). Následující příklad vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* v *eastus* umístění:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Přidat pravidlo s [vytvořit pravidla nsg sítě az](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) povolit provoz protokolu HTTP na svůj webový server (nebo upravit vlastní scénáře, jako je připojení SSH přístupu nebo databáze). Následující příklad vytvoří pravidlo s názvem *myNetworkSecurityGroupRule* umožňující komunikaci TCP na portu 80:

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
Přidružení skupiny zabezpečení sítě Virtuálního počítače síťovým rozhraním (NIC) [aktualizace seskupování sítě az](/cli/azure/network/nic#az_network_nic_update). Následující příklad přiřadí stávající síťové karty s názvem *myNic* ke skupině zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Alternativně můžete přiřadit skupině zabezpečení sítě podsíť virtuální sítě s [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) spíše než jenom k síťové rozhraní na jeden virtuální počítač. Následující příklad přiřadí existující podsíť s názvem *mySubnet* v *myVnet* virtuální síť s skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy umožňují zprovoznění s provoz do virtuálního počítače. Skupiny zabezpečení sítě zadejte mnoho funkcí a členitosti pro řízení přístupu k prostředkům. Další informace o [vytvoření skupiny zabezpečení sítě a seznamu ACL pravidla zde](tutorial-virtual-network.md#secure-network-traffic).

Pro vysokou dostupnost webové aplikace měli byste umístit virtuální počítače za pro vyrovnávání zatížení Azure. Nástroje pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů s skupinu zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [jak načíst vyvážit virtuální počítače s Linuxem v Azure k vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili jednoduché pravidlo umožňující přenos HTTP. Můžete najít informace o vytváření podrobnější prostředí v těchto článcích:

* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
* [Co je skupina zabezpečení sítě (NSG)?](../../virtual-network/security-overview.md)
