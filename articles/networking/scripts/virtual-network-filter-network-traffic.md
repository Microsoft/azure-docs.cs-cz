---
title: Ukázkový skript Azure CLI – Filtrování síťového provozu virtuálního počítače | Microsoft Docs
description: Použijte skript rozhraní příkazového řádku Azure CLI k filtrování příchozího a odchozího síťového provozu virtuálního počítače pomocí front-endové a back-endové podsítě.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 61f2441d68954a167b9887a4dfd4b99a53c14166
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88037214"
---
# <a name="use-an-azure-cli-script-to-filter-inbound-and-outbound-vm-network-traffic"></a>Filtrování příchozího a odchozího síťového provozu virtuálního počítače pomocí skriptu Azure CLI

Tento ukázkový skript vytvoří virtuální síť s front-endovou a back-endovou podsítí. Příchozí síťový provoz do front-endové podsítě je omezený na HTTP, HTTPS a SSH, zatímco odchozí provoz do Internetu z back-endové podsítě není povolený. Po spuštění skriptu budete mít jeden virtuální počítač se dvěma síťovými kartami. Obě síťové karty budou připojené k jiné podsíti.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

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
| [az network subnet create](/cli/azure/network/vnet/subnet) | Vytvoří back-endovou podsíť. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Přidruží skupiny zabezpečení sítě k podsítím. |
| [az network public-ip create](/cli/azure/network/public-ip) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu počítači z Internetu. |
| [az network nic create](/cli/azure/network/nic) | Vytvoří virtuální síťová rozhraní a připojí je k front-endové a back-endové podsíti virtuální sítě. |
| [az network nsg create](/cli/azure/network/nsg) | Vytvoří skupiny zabezpečení sítě (NSG), které se přidruží k front-endové a back-endové podsíti. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Vytvoří pravidla NSG, která povolí nebo zablokují konkrétní porty v konkrétních podsítích. |
| [az vm create](/cli/azure/vm) | Vytvoří virtuální počítače a ke každému z nich připojí síťovou kartu. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další informace o ukázkových skriptech rozhraní příkazového řádku najdete v [dokumentaci Přehled sítí Azure](../cli-samples.md) .
