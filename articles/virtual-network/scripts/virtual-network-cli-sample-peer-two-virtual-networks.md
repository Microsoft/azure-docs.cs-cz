---
title: Partnerské dva virtuální sítě – ukázkový skript Azure CLI
description: Vytvořte a připojte dvě virtuální sítě ve stejné oblasti prostřednictvím sítě Azure pomocí ukázkového skriptu Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6cd6d61ba1404ca6b6de63a0151ec414b2df54e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87492458"
---
# <a name="peer-two-virtual-networks-with-an-azure-cli-script-sample"></a>Partnerský vztah dvou virtuálních sítí s ukázkovým skriptem Azure CLI

Tento ukázkový skript vytvoří dvě virtuální sítě ve stejné oblasti a propojí je prostřednictvím sítě Azure. Po spuštění skriptu budete mít partnerský vztah mezi dvěma virtuálními sítěmi.

Skript můžete spustit ve službě Azure [Cloud Shell](https://shell.azure.com/bash) nebo v místně nainstalovaném Azure CLI. Pokud používáte rozhraní příkazového řádku místně, musíte použít verzi 2.0.28 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli). Pokud používáte rozhraní příkazového řádku místně, je také potřeba spustit příkaz `az login` pro vytvoření připojení k Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](/cli/azure/network/vnet) | Vytvoří virtuální síť Azure a podsíť. |
| [az network vnet peering create](/cli/azure/network/vnet/peering) | Vytvoří partnerský vztah mezi dvěma virtuálními sítěmi.  |
| [az group delete](/cli/azure/vm/extension) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro virtuální síť najdete v tématu [Ukázky rozhraní příkazového řádku pro virtuální síť](../cli-samples.md).
