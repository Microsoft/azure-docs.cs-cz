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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ce6e20e5e4866bc4daa3f331fa2a8612ac6c260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184157"
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
| [AZ Redis DELETE](/cli/azure/redis) | Odstraňte službu Azure cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptů Azure cache pro Redis CLI najdete v [dokumentaci k Azure cache pro Redis](../cli-samples.md).