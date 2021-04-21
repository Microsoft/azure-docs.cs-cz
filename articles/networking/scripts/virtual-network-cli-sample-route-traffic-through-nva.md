---
title: Ukázkový skript Azure CLI – směrování provozu přes síťové virtuální zařízení
description: Ukázkový skript Azure CLI – Směrování provozu přes síťové virtuální zařízení brány firewall
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7970ab4472000c53e23f7962a9cbf4ec05ea3465
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763176"
---
# <a name="use-an-azure-cli-script-to-route-traffic-through-a-network-virtual-appliance"></a>Použití skriptu Azure CLI ke směrování provozu přes síťové virtuální zařízení

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
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet) | Vytvoří virtuální síť Azure a front-endovou podsíť. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Vytvoří back-endovou podsíť a podsíť DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu počítači z Internetu. |
| [az network nic create](/cli/azure/network/nic) | Vytvoří virtuální síťové rozhraní a povolí pro něj předávání IP. |
| [az network nsg create](/cli/azure/network/nsg) | Vytvoří skupinu zabezpečení sítě (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Vytvoří pravidla NSG, která povolí příchozí provoz na portech HTTP a HTTPS do virtuálního počítače. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Přidruží k podsítím skupiny zabezpečení sítě a směrovací tabulky. |
| [az network route-table create](/cli/azure/network/route-table#az_network_route_table_create)| Vytvoří směrovací tabulku pro všechny trasy. |
| [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create)| Vytvoří trasy pro směrování provozu mezi podsítěmi a internetem přes virtuální počítač. |
| [az vm create](/cli/azure/vm) | Vytvoří virtuální počítač a připojí k němu síťovou kartu. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další informace o ukázkových skriptech rozhraní příkazového řádku najdete v [dokumentaci Přehled sítí Azure](../cli-samples.md) .
