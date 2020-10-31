---
title: Vytvořit zámek prostředků pro Cassandra prostor a tabulku pro Azure Cosmos DB
description: Vytvořit zámek prostředků pro Cassandra prostor a tabulku pro Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 54f291d46c073f3685f5ac80f194077f765480a2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086614"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>Vytvoření zámku prostředků pro Azure Cosmos rozhraní API Cassandraho prostoru a tabulky pomocí Azure CLI
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.9.1 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Zámky prostředků nefungují pro změny provedené uživateli, kteří se připojují pomocí Cassandra SDK, CQL shellu nebo webu Azure Portal, pokud se účet Cosmos DB nejdříve nezamkne s `disableKeyBasedMetadataWriteAccess` povolenou vlastností. Další informace o tom, jak tuto vlastnost Povolit, najdete v tématu [prevence změn ze sad SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Lock Create](/cli/azure/lock#az-lock-create) | Vytvoří zámek. |
| [AZ Lock list](/cli/azure/lock#az-lock-list) | Vypíše informace o zámku. |
| [AZ Lock show](/cli/azure/lock#az-lock-show) | Zobrazit vlastnosti zámku. |
| [AZ LOCK Delete](/cli/azure/lock#az-lock-delete) | Odstraní zámek. |

## <a name="next-steps"></a>Další kroky

-[Uzamknout prostředky, aby nedocházelo k neočekávaným změnám](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku.

-[Azure Cosmos DB úložiště GitHubu CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
