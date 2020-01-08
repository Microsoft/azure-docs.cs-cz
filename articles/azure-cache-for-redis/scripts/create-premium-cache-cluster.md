---
title: Vytvoření mezipaměti Azure Premium pro Redis s clusteringem – Azure CLI
description: Tento ukázkový kód Azure CLI ukazuje, jak vytvořit mezipaměť Azure úrovně Premium pro Redis s povoleným clusteringem a dvěma horizontálních oddílů.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411144"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Vytvoření Azure Cache úrovně Premium pro Redis s clusteringem

V tomto scénáři se dozvíte, jak k vytvoření Azure Cache úrovně Premium 6 GB pro s aktivovaným clusteringem Redis a dvě horizontálních oddílů.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků používá následující příkazy a povolení mezipaměti Redis Azure úrovně Premium s clusteringem. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Vytvoření az redis](https://docs.microsoft.com/cli/azure/redis) | Vytvoření Azure Cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).
