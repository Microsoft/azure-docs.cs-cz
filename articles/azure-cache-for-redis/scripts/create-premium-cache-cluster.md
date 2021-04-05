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
ms.custom: devx-track-azurecli
ms.openlocfilehash: bb5a6ac082ebaf978023321f15341ec7f35779a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184211"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Vytvoření mezipaměti Azure Premium pro Redis s clusteringem

V tomto scénáři se dozvíte, jak vytvořit mezipaměť Azure úrovně Premium pro Redis s povoleným clusteringem a dvěma horizontálních oddílů.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření skupiny prostředků a mezipaměti Azure úrovně Premium pro Redis s povolením clusteringu. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [AZ Redis Create](/cli/azure/redis) | Vytvořte mezipaměť Azure pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptů Azure cache pro Redis CLI najdete v [dokumentaci k Azure cache pro Redis](../cli-samples.md).