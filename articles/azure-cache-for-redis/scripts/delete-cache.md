---
title: Odstranění mezipaměti Azure pro Redis – Azure CLI
description: Tento ukázkový kód Azure CLI ukazuje, jak odstranit Azure cache pro instanci Redis pomocí příkazu AZ Redis DELETE.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: e5178ab4069cc5ffa8607c5feea6ffac86284a5b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411084"
---
# <a name="delete-an-azure-cache-for-redis"></a>Odstranění mezipaměti Azure pro Redis

V tomto scénáři se dozvíte, jak odstranit Azure cache pro Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů odstraní službu Azure cache for Redis instance. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Redis DELETE](https://docs.microsoft.com/cli/azure/redis) | Odstraňte službu Azure cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptů Azure cache pro Redis CLI najdete v [dokumentaci k Azure cache pro Redis](../cli-samples.md).
