---
title: Ukázkový skript Azure CLI – načtení podrobností Azure pro Redis Cache | Dokumentace Microsoftu
description: Ukázkový skript Azure CLI – načtení podrobností mezipaměti Azure Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 0dd32ae2b8f0fb91ca8d6391d5c3f3979d13b90f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085259"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Získat podrobnosti mezipaměti Azure pro Redis

V tomto scénáři se dozvíte, jak k načtení podrobností mezipaměti Azure pro instanci Redis, včetně jeho stav zřizování.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k načtení podrobností mezipaměti Azure pro Redis instance. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Načtěte Podrobnosti mezipaměti Azure pro Redis instance. |


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).