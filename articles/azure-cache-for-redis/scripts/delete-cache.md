---
title: Odstranění mezipaměti Azure pro Redis – nastavení příkazu k onomu Azure
description: Tato ukázka kódu velmocí azure ukazuje, jak odstranit azure cache pro redis instance pomocí příkazu az redis delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: e5178ab4069cc5ffa8607c5feea6ffac86284a5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411084"
---
# <a name="delete-an-azure-cache-for-redis"></a>Odstranění mezipaměti Azure pro Redis

V tomto scénáři se dozvíte, jak odstranit mezipaměť Azure pro Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k odstranění instance Azure Cache for Redis. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az redis odstranit](https://docs.microsoft.com/cli/azure/redis) | Odstraňte azure cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu Azure Cache for Redis CLI najdete v [dokumentaci k Azure Cache for Redis](../cli-samples.md).
