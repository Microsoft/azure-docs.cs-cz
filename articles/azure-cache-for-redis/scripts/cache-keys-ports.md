---
title: Získání názvu hostitele, portů, klíčů – Azure cache pro Redis – Azure CLI
description: Tento ukázkový kód Azure CLI ukazuje, jak získat název hostitele, porty a klíče pro instanci služby Azure cache pro Redis.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411299"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Získání názvu hostitele, porty a klíče pro Azure Cache pro Redis

V tomto scénáři se dozvíte, jak načíst název hostitele, porty a klíče používané pro připojení k Azure pro instanci Redis Cache.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy pro načtení názvu hostitele, klíče a porty Azure pro instanci Redis Cache. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Načtěte Podrobnosti mezipaměti Azure pro Redis instance. |
| [AZ redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Získání přístupových klíčů pro Azure Cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).
