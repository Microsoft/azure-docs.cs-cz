---
title: Vytvoření zámku prostředků pro Azure Cosmos DB tabulku rozhraní API pro tabulky
description: Vytvoření zámku prostředků pro Azure Cosmos DB tabulku rozhraní API pro tabulky
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 6df5f3842ba08b04dd82910d3762472f4ff4febd
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566805"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Vytvoření zámku prostředků pro Azure Cosmos DB tabulku rozhraní API pro tabulky pomocí Azure CLI
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.9.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli připojujícími se Cosmos DB tabulek SDK, Azure Storage Table SDK, jakýchkoli nástrojů, které se připojují prostřednictvím klíčů účtu nebo webu Azure Portal, pokud není účet Cosmos DB nejdříve zamčený s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Lock Create](/cli/azure/lock#az-lock-create) | Vytvoří zámek. |
| [AZ Lock list](/cli/azure/lock#az-lock-list) | Vypíše informace o zámku. |
| [AZ Lock show](/cli/azure/lock#az-lock-show) | Zobrazit vlastnosti zámku. |
| [AZ LOCK Delete](/cli/azure/lock#az-lock-delete) | Odstraní zámek. |

## <a name="next-steps"></a>Další kroky

- [Zamknutí prostředků, aby se zabránilo neočekávaným změnám](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku.

- [Azure Cosmos DB úložiště GitHubu CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
