---
title: Ukázky Azure CLI – vytvoření sady škálování virtuálních počítačů
description: Tento skript vytvoří škálovací sadu virtuálních počítačů Azure s operačním systémem Ubuntu a související síťové prostředky, včetně nástroje pro vyrovnávání zatížení.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 97fc21f111e9b95fb9645fd042e47cc53b0af42c
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499700"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Vytvoření škálovací sady virtuálních počítačů pomocí Azure CLI
Tento skript vytvoří škálovací sadu virtuálních počítačů Azure s operačním systémem Ubuntu a související síťové prostředky, včetně nástroje pro vyrovnávání zatížení. Po spuštění skriptu můžete k instancím virtuálních počítačů přistupovat přes SSH.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Spuštěním následujícího příkazu odeberte skupinu prostředků, škálovací sadu a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript k vytvoření skupiny prostředků, škálovací sady virtuálních počítačů a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/ad/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az vmss create](/cli/azure/vmss) | Vytvoří škálovací sadu virtuálních počítačů a připojí ji k virtuální síti, podsíti a skupině zabezpečení sítě. Za účelem distribuce provozu do několika instancí virtuálních počítačů se vytvoří také nástroj pro vyrovnávání zatížení. Tento příkaz také určuje image virtuálního počítače, která se má použít, a přihlašovací údaje pro správu.  |
| [az group delete](/cli/azure/ad/group) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure/overview).
