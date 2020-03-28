---
title: Vytvoření prémiové mezipaměti Azure pro Redis s clusteringem – Azure CLI
description: Tato ukázka kódu rozhraní příkazového příkazu Azure ukazuje, jak vytvořit 6 GB premium úrovně Azure Cache pro Redis s povoleným clustering a dvěma úlomky.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411144"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Vytvoření prémiové mezipaměti Azure pro redis pomocí clusteringu

V tomto scénáři se dozvíte, jak vytvořit 6 GB premium úroveň Azure Cache pro Redis s povolenoclusterování a dva oddíly.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření skupiny prostředků a premium vrstvy Azure Cache pro Redis s clustering povolit. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az redis vytvořit](https://docs.microsoft.com/cli/azure/redis) | Vytvořte azure cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu Azure Cache for Redis CLI najdete v [dokumentaci k Azure Cache for Redis](../cli-samples.md).
