---
title: Ukázkový skript Azure CLI – partnerských dvou virtuálních sítí | Microsoft Docs
description: Pomocí ukázkového skriptu Azure CLI můžete vytvořit a propojit dvě virtuální sítě ve stejné oblasti prostřednictvím sítě Azure.
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
ms.openlocfilehash: 507e17b2edb0db2d21265f3e945745d97395e7c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503850"
---
# <a name="use-an-azure-cli-sample-script-to-connect-two-virtual-networks"></a>Připojení dvou virtuálních sítí pomocí ukázkového skriptu Azure CLI

Tento skript vytvoří a spojí dvě virtuální sítě ve stejné oblasti prostřednictvím sítě Azure. Po spuštění skriptu vytvoříte partnerský vztah mezi dvěma virtuálními sítěmi.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, virtuálního počítače a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Vytvoří virtuální síť Azure a podsíť. |
| [az network vnet peering create](https://docs.microsoft.com/cli/azure/network/vnet/peering) | Vytvoří partnerský vztah mezi dvěma virtuálními sítěmi.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty CLI pro sítě najdete v [dokumentaci se základními informacemi k sítím Azure](../cli-samples.md).
