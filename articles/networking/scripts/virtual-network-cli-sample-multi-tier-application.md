---
title: "Azure CLI skriptu ukázkové – vytvoření sítě pro vícevrstvé aplikace | Microsoft Docs"
description: "Azure CLI skriptu ukázkové – vytvoření virtuální sítě pro vícevrstvé aplikace."
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
ms.openlocfilehash: aa5fef6e8910a5b0b5fe89d5c8cfb141415d07a6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-network-for-multi-tier-applications"></a>Vytvoření sítě pro vícevrstvé aplikace

Tento ukázkový skript vytvoří virtuální síť s podsítí front-end a back-end. Provoz do front-endu podsítě je omezen na protokolu HTTP a SSH, zatímco provozu do podsítě back-end je omezený na MySQL, port 3306. Po spuštění skriptu, budete mít dva virtuální počítače, jeden v jednotlivých podsítích, kterou můžete nasadit webový server a databáze MySQL software.

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

Tento skript používá následující příkazy k vytvoření skupiny prostředků, virtuální sítě a skupiny zabezpečení sítě. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Vytvoření sítě vnet az](/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a front-end podsítě. |
| [Vytvoření podsítě az sítě](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Vytvoří podsíť back-end. |
| [Vytvoření veřejné sítě az-ip](/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu z Internetu přístup k virtuálnímu počítači. |
| [Vytvoření az síťových adaptérů sítě](/cli/azure/network/nic#az_network_nic_create) | Vytvoří rozhraní virtuální sítě a připojí je k podsítím virtuální sítě front-end a back-end. |
| [Vytvoření az sítě nsg](/cli/azure/network/nsg#az_network_nsg_create) | Vytvoří síť skupiny zabezpečení (NSG), které jsou přidruženy k podsítím front-end a back-end. |
| [Vytvoření pravidla nsg az sítě](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Vytvoří pravidla NSG, které povolí nebo blokuje specifické porty ke konkrétním podsítím. |
| [Vytvoření virtuálního počítače az](/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítače a připojí síťovou kartu pro každý virtuální počítač. Tento příkaz také Určuje bitovou kopii virtuálního počítače používat a pověření pro správu. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další síťové ukázky skriptu rozhraní příkazového řádku najdete v [dokumentace přehled sítě Azure](../cli-samples.md)