---
title: "Ukázka skriptu Azure CLI - provoz sítě virtuálních počítačů filtr | Microsoft Docs"
description: "Azure CLI skriptu ukázkové – filtrovat příchozí a odchozí provoz sítě virtuálních počítačů."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: jdial
ms.openlocfilehash: 07211d7b6dccd377f94308da5c572255ba5727c6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Filtrovat příchozí a odchozí provoz sítě virtuálních počítačů

Tento ukázkový skript vytvoří virtuální síť s podsítí front-end a back-end. Příchozí síťový provoz do front-endu podsíť je omezený na protokolu HTTP, HTTPS a SSH, zatímco odchozí provoz k Internetu z podsítě back-end není povolen. Po spuštění skriptu, budete mít jeden virtuální počítač se dvěma síťovými adaptéry. Každý síťový adaptér je připojený k jiné podsíti.

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

Tento skript používá následující příkazy k vytvoření skupiny prostředků, virtuální sítě a skupiny zabezpečení sítě. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a front-end podsítě. |
| [Vytvoření podsítě az sítě](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Vytvoří podsíť back-end. |
| [aktualizace az sítě vnet podsíť](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Přidruží skupiny Nsg na podsítě. |
| [Vytvoření veřejné sítě az-ip](/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu z Internetu přístup k virtuálnímu počítači. |
| [Vytvoření az síťových adaptérů sítě](/cli/azure/network/nic#az_network_nic_create) | Vytvoří rozhraní virtuální sítě a připojí je k podsítím virtuální sítě front-end a back-end. |
| [Vytvoření az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create) | Vytvoří síť skupiny zabezpečení (NSG), které jsou přidruženy k podsítím front-end a back-end. |
| [Vytvoření pravidla nsg az sítě](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Vytvoří pravidla NSG, které povolí nebo blokuje specifické porty ke konkrétním podsítím. |
| [Vytvoření virtuálního počítače az](/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítače a připojí síťovou kartu pro každý virtuální počítač. Tento příkaz také Určuje bitovou kopii virtuálního počítače používat a pověření pro správu. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další síťové ukázky skriptu rozhraní příkazového řádku najdete v [dokumentace přehled sítě Azure](../cli-samples.md)