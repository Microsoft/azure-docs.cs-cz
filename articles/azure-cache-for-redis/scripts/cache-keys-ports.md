---
title: Získejte název hostitele, porty, klíče – Azure Cache for Redis – Azure CLI
description: Tento kód Vzak Azure ukázka ukazuje, jak získat název hostitele, porty a klíče pro azure cache pro redis instance.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411299"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Získání názvu hostitele, portů a klíčů pro Azure Cache for Redis

V tomto scénáři se dozvíte, jak načíst název hostitele, porty a klíče používané pro připojení k instanci Azure Cache for Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k načtení názvu hostitele, klíčů a portů instance Azure Cache for Redis. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Načíst podrobnosti o azure mezipaměti pro redis instance. |
| [az redis seznam-klíče](https://docs.microsoft.com/cli/azure/redis) | Načíst přístupové klíče pro instanci Azure Cache for Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu Azure Cache for Redis CLI najdete v [dokumentaci k Azure Cache for Redis](../cli-samples.md).
