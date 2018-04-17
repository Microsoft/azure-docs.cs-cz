---
title: Ukázkový skript Azure CLI – Filtrování síťového provozu virtuálního počítače | Microsoft Docs
description: Ukázkový skript Azure CLI – Filtrování příchozího a odchozího síťového provozu virtuálního počítače
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 8e667b2ffd8820b78cbba560724527ba2c2b3bc4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-script-sample"></a>Ukázkový skript pro filtrování příchozího a odchozího síťového provozu virtuálního počítače

Tento ukázkový skript vytvoří virtuální síť s front-endovou a back-endovou podsítí. Příchozí síťový provoz do front-endové podsítě je omezený na HTTP, HTTPS a SSH, zatímco odchozí provoz do internetu z back-endové podsítě není omezený. Po spuštění skriptu budete mít jeden virtuální počítač se dvěma síťovými kartami. Obě síťové karty budou připojené k jiné podsíti.

Skript můžete spustit ve službě Azure [Cloud Shell](https://shell.azure.com/bash) nebo v místně nainstalovaném Azure CLI. Pokud používáte rozhraní příkazového řádku místně, musíte použít verzi 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte rozhraní příkazového řádku místně, je také potřeba spustit příkaz `az login` pro vytvoření připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuální sítě a skupin zabezpečení sítě používá následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a front-endovou podsíť. |
| [az network subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Vytvoří back-endovou podsíť. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Přidruží skupiny zabezpečení sítě k podsítím. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Vytvoří veřejnou IP adresu pro přístup k virtuálnímu počítači z internetu. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Vytvoří virtuální síťová rozhraní a připojí je k front-endové a back-endové podsíti virtuální sítě. |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Vytvoří skupiny zabezpečení sítě (NSG), které se přidruží k front-endové a back-endové podsíti. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |Vytvoří pravidla NSG, která povolí nebo zablokují konkrétní porty v konkrétních podsítích. |
| [az vm create](/cli/azure/vm#az_vm_create) | Vytvoří virtuální počítače a ke každému z nich připojí síťovou kartu. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků a všechny prostředky, které obsahuje. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální síť najdete v tématu [Ukázky rozhraní příkazového řádku pro virtuální síť](../cli-samples.md).
