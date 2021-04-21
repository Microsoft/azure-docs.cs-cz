---
title: Vytvoření zámku prostředků pro databázi Gremlin a graf pro Azure Cosmos DB
description: Vytvoření zámku prostředků pro databázi Gremlin a graf pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 19809d7150c8a461f97282d1583d0d870d6af8bb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770646"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>Vytvoření zámku prostředků pro databázi a graf Azure Cosmos Gremlin API pomocí Azure CLI
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.9.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří se připojují pomocí Gremlin SDK nebo webu Azure Portal, pokud není účet Cosmos DB nejdříve zamčený s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Lock Create](/cli/azure/lock#az_lock_create) | Vytvoří zámek. |
| [AZ Lock list](/cli/azure/lock#az_lock_list) | Vypíše informace o zámku. |
| [AZ Lock show](/cli/azure/lock#az_lock_show) | Zobrazit vlastnosti zámku. |
| [AZ LOCK Delete](/cli/azure/lock#az_lock_delete) | Odstraní zámek. |

## <a name="next-steps"></a>Další kroky

- [Zamknutí prostředků, aby se zabránilo neočekávaným změnám](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku.

- [Azure Cosmos DB úložiště GitHubu CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
