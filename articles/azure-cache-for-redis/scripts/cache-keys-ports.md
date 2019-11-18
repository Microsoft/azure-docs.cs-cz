---
title: Ukázkový skript Azure CLI – získání názvu hostitele, portů a klíčů pro Azure cache pro Redis
description: Skript Azure CLI ukázkový – získání názvu hostitele, porty a klíče pro Azure Cache pro instanci Redis
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 604ec6de3b95a4bc289176d54d9c7b0a6c42eae6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122519"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Získání názvu hostitele, porty a klíče pro Azure Cache pro Redis

V tomto scénáři se dozvíte, jak načíst název hostitele, porty a klíče používané pro připojení k Azure pro instanci Redis Cache.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy pro načtení názvu hostitele, klíče a porty Azure pro instanci Redis Cache. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky: |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Načtěte Podrobnosti mezipaměti Azure pro Redis instance. |
| [AZ redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Získání přístupových klíčů pro Azure Cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).
