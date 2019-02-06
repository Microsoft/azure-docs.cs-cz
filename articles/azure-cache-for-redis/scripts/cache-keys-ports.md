---
title: Skript Azure CLI ukázkový – získání názvu hostitele, porty a klíče pro Azure Cache pro Redis | Dokumentace Microsoftu
description: Skript Azure CLI ukázkový – získání názvu hostitele, porty a klíče pro Azure Cache pro instanci Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fd69c3182f026996618fea5e26466e8434ea36f6
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754451"
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


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).
