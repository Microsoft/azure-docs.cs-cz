---
title: Směrování provozu přes síťové virtuální zařízení – ukázkový skript Azure CLI
description: Směrování provozu přes síťové virtuální zařízení brány firewall – ukázkový skript Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 964fda8168867c115502c7262dc1d41e55075866
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317646"
---
# <a name="route-traffic-through-a-network-virtual-appliance---azure-cli-script-sample"></a>Směrování provozu přes síťové virtuální zařízení – ukázkový skript Azure CLI

Tento ukázkový skript vytvoří virtuální síť s front-endovou a back-endovou podsítí. Vytvoří také virtuální počítač s povoleným předáváním IP pro směrování provozu mezi těmito dvěma podsítěmi. Po spuštění skriptu budete na virtuální počítač moct nasadit síťový software, například aplikaci brány firewall.

Skript můžete spustit ve službě Azure [Cloud Shell](https://shell.azure.com/bash) nebo v místně nainstalovaném Azure CLI. Pokud používáte rozhraní příkazového řádku místně, musíte použít verzi 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Pokud používáte rozhraní příkazového řádku místně, je také potřeba spustit příkaz `az login` pro vytvoření připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuální sítě a skupin zabezpečení sítě používá následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet) | Vytvoří virtuální síť Azure a front-endovou podsíť. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Vytvoří back-endovou podsíť a podsíť DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu počítači z internetu. |
| [az network nic create](/cli/azure/network/nic) | Vytvoří virtuální síťové rozhraní a povolí pro něj předávání IP. |
| [az network nsg create](/cli/azure/network/nsg) | Vytvoří skupinu zabezpečení sítě (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Vytvoří pravidla NSG, která povolí příchozí provoz na portech HTTP a HTTPS do virtuálního počítače. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Přidruží k podsítím skupiny zabezpečení sítě a směrovací tabulky. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Vytvoří směrovací tabulku pro všechny trasy. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Vytvoří trasy pro směrování provozu mezi podsítěmi a internetem přes virtuální počítač. |
| [az vm create](/cli/azure/vm) | Vytvoří virtuální počítač a připojí k němu síťovou kartu. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální síť najdete v tématu [Ukázky rozhraní příkazového řádku pro virtuální síť](../cli-samples.md).
