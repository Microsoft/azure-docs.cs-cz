---
title: Ukázky Azure Azure CLI pro Redis
description: 'Ukázky v Azure CLI pro službu Azure cache pro Redis: Vytvořte mezipaměť, odstraňte mezipaměť, Získejte podrobnosti mezipaměti, název hostitele, porty a klíče a připojte webovou aplikaci.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: 43a986f884d621f257de2e9c305a0bcc59092d3d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87033661"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Ukázky v Azure CLI pro Azure cache pro Redis

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| Vytvoření mezipaměti | Popis |
| ------------ | ----------- |
| [Vytvoření mezipaměti](./scripts/create-cache.md) | Vytvoří skupinu prostředků a Azure cache úrovně Basic pro Redis. |
| [Vytvoření mezipaměti úrovně Premium s clusteringem](./scripts/create-premium-cache-cluster.md) | Vytvoří skupinu prostředků a mezipaměť úrovně Premium s povoleným clusteringem.|
| [Získat podrobnosti mezipaměti](./scripts/show-cache.md) | Získá podrobnosti o službě Azure cache pro instanci Redis, včetně stavu zřizování. |
| [Získat název hostitele, porty a klíče](./scripts/cache-keys-ports.md) | Získá název hostitele, porty a klíče pro instanci služby Azure cache pro Redis. |
|**Webová aplikace plus mezipaměť**| **Popis**|
| [Připojení webové aplikace k mezipaměti Azure pro Redis](./../app-service/scripts/cli-connect-to-redis.md) | Vytvoří webovou aplikaci Azure a mezipaměť Azure pro Redis a potom do nastavení aplikace přidá podrobnosti o připojení Redis. |
|**Odstranit mezipaměť**| **Popis** |
| [Odstranění mezipaměti](./scripts/delete-cache.md) | Odstraní službu Azure cache for Redis instance.  |

Další informace o rozhraní příkazového řádku Azure najdete v tématu věnovaném [instalaci Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
