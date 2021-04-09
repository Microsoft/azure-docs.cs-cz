---
title: Získání podrobností o službě Azure cache pro Redis – Azure CLI
description: Tento ukázkový kód Azure CLI ukazuje, jak načíst podrobnosti o instanci Azure cache for Redis, včetně jejího stavu zřízení.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8901847a1c214b257243c4da0a74897061409bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184242"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Získat podrobnosti o službě Azure cache pro Redis

V tomto scénáři se dozvíte, jak načíst podrobnosti o instanci služby Azure cache pro Redis, včetně jejího stavu zřízení.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k načtení podrobností o službě Azure cache pro instanci Redis. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Redis show](/cli/azure/redis) | Načte podrobnosti instance Azure cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptů Azure cache pro Redis CLI najdete v [dokumentaci k Azure cache pro Redis](../cli-samples.md).