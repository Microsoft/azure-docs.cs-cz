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
ms.openlocfilehash: 7bcdd999a4954766398800e6e6a0ddb8c9727a99
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749155"
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
| [AZ redis show](https://docs.microsoft.com/cli/azure/redis) | Načtěte Podrobnosti mezipaměti Azure pro Redis instance. |


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).
