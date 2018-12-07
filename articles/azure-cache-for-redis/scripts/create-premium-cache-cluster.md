---
title: Skript Azure CLI ukázkový – vytvoření Azure Cache úrovně Premium pro Redis s clusteringem | Dokumentace Microsoftu
description: Skript Azure CLI ukázkový – vytvoření Azure Cache úrovně Premium pro Redis s clusteringem
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 727af1b64f43189d3cfcee39d10bfb6920323ffb
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019344"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Vytvoření Azure Cache úrovně Premium pro Redis s clusteringem

V tomto scénáři se dozvíte, jak k vytvoření Azure Cache úrovně Premium 6 GB pro s aktivovaným clusteringem Redis a dvě horizontálních oddílů.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků používá následující příkazy a povolení mezipaměti Redis Azure úrovně Premium s clusteringem. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Vytvoření az redis](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Vytvoření Azure Cache pro instanci Redis. |


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).