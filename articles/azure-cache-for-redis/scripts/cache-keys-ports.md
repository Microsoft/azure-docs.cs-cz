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
ms.custom: devx-track-azurecli
ms.openlocfilehash: d3e8e359a97c091e025049ac8a978e1beca1d759
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184212"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Získat název hostitele, porty a klíče pro Azure cache pro Redis

V tomto scénáři se dozvíte, jak načíst název hostitele, porty a klíče používané pro připojení k instanci Azure cache pro Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů načte název hostitele, klíče a porty Azure cache pro instanci Redis. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Redis show](/cli/azure/redis) | Načte podrobnosti instance Azure cache pro instanci Redis. |
| [AZ Redis list-Keys](/cli/azure/redis) | Načte přístupové klíče pro instanci Azure cache pro Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptů Azure cache pro Redis CLI najdete v [dokumentaci k Azure cache pro Redis](../cli-samples.md).