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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77a03ac7ca47e3ba40242adfbd5d57d9dbd21f30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184140"
---
# <a name="create-an-azure-cache-for-redis"></a>Vytvoření instance Azure Cache for Redis

V tomto scénáři se naučíte vytvořit mezipaměť Azure pro Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků a mezipaměti Azure pro Redis používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [AZ Redis Create](/cli/azure/redis) | Vytvořte mezipaměť Azure pro instanci Redis. |


## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázky skriptů Azure cache pro Redis CLI najdete v [dokumentaci k Azure cache pro Redis](../cli-samples.md).