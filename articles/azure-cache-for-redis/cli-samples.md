---
title: Správa Azure cache pro Redis pomocí Azure CLI
description: 'Ukázky v Azure CLI pro správu mezipaměti Azure pro Redis: Vytvořte mezipaměť, odstraňte mezipaměť, Získejte podrobnosti mezipaměti, název hostitele, porty a klíče a připojte webovou aplikaci.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9bfdd2d03b3ab6edd04a641787475930435a9ffc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87499598"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Správa Azure cache pro Redis pomocí Azure CLI

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| Vytvoření mezipaměti | Description |
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
