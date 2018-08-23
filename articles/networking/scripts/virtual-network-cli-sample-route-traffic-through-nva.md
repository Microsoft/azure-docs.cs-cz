---
title: Ukázkový skript Azure CLI – Směrování provozu přes síťové virtuální zařízení | Microsoft Docs
description: Ukázkový skript Azure CLI – Směrování provozu přes síťové virtuální zařízení brány firewall
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 62077f45d96e96a7fef35cf025740849d2b99445
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42060533"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Směrování provozu přes síťové virtuální zařízení

Tento ukázkový skript vytvoří virtuální síť s front-endovou a back-endovou podsítí. Vytvoří také virtuální počítač s povoleným předáváním IP pro směrování provozu mezi těmito dvěma podsítěmi. Po spuštění skriptu budete na virtuální počítač moct nasadit síťový software, například aplikaci brány firewall.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Ukázkový skript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuální sítě a skupin zabezpečení sítě používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a front-endovou podsíť. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Vytvoří back-endovou podsíť a podsíť DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu počítači z Internetu. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Vytvoří virtuální síťové rozhraní a povolí pro něj předávání IP. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Vytvoří skupinu zabezpečení sítě (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Vytvoří pravidla NSG, která povolí příchozí provoz na portech HTTP a HTTPS do virtuálního počítače. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)| Přidruží k podsítím skupiny zabezpečení sítě a směrovací tabulky. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Vytvoří směrovací tabulku pro všechny trasy. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Vytvoří trasy pro směrování provozu mezi podsítěmi a Internetem přes virtuální počítač. |
| [az vm create](/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítač a připojí k němu síťovou kartu. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další sítě ukázkové skripty rozhraní příkazového řádku najdete v [dokumentaci přehled sítě Azure](../cli-samples.md)