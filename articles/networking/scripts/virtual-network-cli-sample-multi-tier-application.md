---
title: Ukázka skriptu Azure CLI – vytvoření sítě pro vícevrstvé aplikace
description: Ukázkový skript Azure CLI – Vytvoření virtuální sítě pro vícevrstvé aplikace
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
ms.openlocfilehash: 97e26eaed72bc15ebc46a893d269b05484d8a064
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521538"
---
# <a name="create-a-network-for-multi-tier-applications"></a>Vytvoření sítě pro vícevrstvé aplikace

Tento ukázkový skript vytvoří virtuální síť s front-endovou a back-endovou podsítí. Provoz do front-endové podsítě je omezený na HTTP a SSH, zatímco provoz do back-endové podsítě je omezený na MySQL na portu 3306. Po spuštění skriptu budete mít dva virtuální počítače, v každé podsíti jeden, na které můžete nasadit webový server a software MySQL.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Ukázkový skript


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.sh  "Virtual network for multi-tier application")]

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
| [az network public-ip create](/cli/azure/network/public-ip) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu virtuálnímu počítačům z Internetu. |
| [az network nic create](/cli/azure/network/nic) | Vytvoří virtuální síťová rozhraní a připojí je k front-endové a back-endové podsíti virtuální sítě. |
| [az network nsg create](/cli/azure/network/nsg) | Vytvoří skupiny zabezpečení sítě (NSG), které se přidruží k front-endové a back-endové podsíti. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Vytvoří pravidla NSG, která povolí nebo zablokují konkrétní porty v konkrétních podsítích. |
| [az vm create](/cli/azure/vm) | Vytvoří virtuální počítače a ke každému z nich připojí síťovou kartu. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptů příkazového příkazu k síti najdete v [dokumentaci k přehledu sítí Azure](../cli-samples.md)
