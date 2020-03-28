---
title: Získejte podrobnosti o azure mezipaměti pro Redis – Azure CLI
description: Tato ukázka kódu vyvedení účtu Azure ukazuje, jak načíst podrobnosti o azure mezipaměti pro instanci Redis, včetně jeho stavu zřizování.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 54920017f4a0598a74a54114e3d1e0a5392d7d2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411056"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Získání podrobností o Azure Cache pro Redis

V tomto scénáři se dozvíte, jak načíst podrobnosti azure mezipaměti pro redis instance, včetně jeho stavu zřizování.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k načtení podrobností o instanci Azure Cache for Redis. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Načíst podrobnosti o azure mezipaměti pro redis instance. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu Azure Cache for Redis CLI najdete v [dokumentaci k Azure Cache for Redis](../cli-samples.md).
