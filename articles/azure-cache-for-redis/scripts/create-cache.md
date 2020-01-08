---
title: Vytvoření mezipaměti Azure pro Redis – Azure CLI
description: Tento ukázkový kód Azure CLI ukazuje, jak vytvořit mezipaměť Azure pro instanci Redis pomocí příkazu AZ Redis Create.
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 79b749c0d02a21c1225ee0d046d73ed3fdd98904
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411319"
---
# <a name="create-an-azure-cache-for-redis"></a>Vytvoření Azure Cache pro Redis

V tomto scénáři se dozvíte, jak k vytvoření Azure Cache pro Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření skupiny prostředků a Azure Cache pro Redis. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Vytvoření az redis](https://docs.microsoft.com/cli/azure/redis) | Vytvoření Azure Cache pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další mezipaměti Azure pro ukázkové skripty rozhraní příkazového řádku redis Cache najdete v [mezipaměti Azure Redis dokumentaci](../cli-samples.md).
