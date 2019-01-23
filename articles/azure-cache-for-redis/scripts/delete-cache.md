---
title: Ukázkový skript Azure CLI – odstranění mezipaměti Azure redis Cache | Dokumentace Microsoftu
description: Ukázkový skript Azure CLI – odstranění Azure mezipaměti Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 4a7b239010d57e3f352ee29afe267273391ffbcd
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461820"
---
# <a name="delete-an-azure-cache-for-redis"></a>Odstranění Azure mezipaměti Redis

V tomto scénáři se dozvíte, jak odstranit Azure Cache pro Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy odstranění mezipaměti Azure pro Redis instance. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ redis delete](https://docs.microsoft.com/cli/azure/redis) | Odstranění mezipaměti Azure pro Redis instance. |


## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).
