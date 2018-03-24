---
title: Ukázka skriptu Azure CLI - sdílené dvě virtuální sítě | Microsoft Docs
description: Azure CLI ukázka skriptu - sdílené dvě virtuální sítě.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 244d7f6ff64643386c417d708f7fb1e9bbc34209
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="peer-two-virtual-networks"></a>Peer dvě virtuální sítě

Tento skript vytvoří a připojí dvou virtuálních sítí ve stejné oblasti přes síť Azure. Po spuštění skriptu, máte partnerský vztah mezi dvěma virtuálními sítěmi.

Můžete spustit skript z Azure [cloudové prostředí](https://shell.azure.com/bash), nebo z místní instalace rozhraní příkazového řádku Azure. Pokud používáte rozhraní příkazového řádku místně, tento skript vyžaduje, že používáte verzi 2.0.28 nebo novější. Chcete-li najít nainstalovanou verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte rozhraní příkazového řádku místně, musíte také spustit `az login` vytvořit připojení s Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spusťte následující příkaz pro odebrání skupiny prostředků, virtuální počítač a všechny související prostředky:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v následující tabulce odkazy na dokumentaci specifické pro příkaz:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Vytvoří virtuální síť Azure a podsíť. |
| [virtuální síť az sítě partnerský vztah vytvořit.](/cli/azure/network/vnet/peering#az_network_vnet_peering_create) | Vytvoří partnerský vztah mezi dvěma virtuálními sítěmi.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další virtuální síťové rozhraní příkazového řádku skriptu ukázky lze nalézt v [virtuální sítě rozhraní příkazového řádku ukázky](../cli-samples.md).
