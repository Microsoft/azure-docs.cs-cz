---
title: "Ukázka skriptu Azure CLI - Get název hostitele, porty a klíče pro Azure Redis Cache | Microsoft Docs"
description: "Ukázka skriptu Azure CLI - Get název hostitele, porty a klíče pro Azure Redis Cache instance"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 9a2a6a7ed4bb56f93c965a5bfd24b21368117c7b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Získat název hostitele, porty a klíče pro Azure Redis Cache

V tomto scénáři zjistěte, jak načíst název hostitele, porty a klíče, které slouží k připojení k instanci služby Azure Redis Cache.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy pro načtení názvu hostitele, klíče a porty instanci služby Azure Redis Cache. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Zobrazit az redis](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Načtěte podrobnosti o instanci služby Azure Redis Cache. |
| [AZ redis seznamu klíčů](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Načtěte přístupové klíče pro instanci služby Azure Redis Cache. |


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu Azure Redis Cache rozhraní příkazového řádku najdete v [dokumentace k Azure Redis Cache](../cli-samples.md).