---
title: Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos
description: Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4a1a061945fe1c6c6a95eb62d286d40a158281ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770730"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Přidání oblastí, změna priority převzetí služeb při selhání, aktivace převzetí služeb při selhání pro účet Azure Cosmos pomocí Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.9.1 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

Tento skript ukazuje tři operace.

- Přidejte oblast do existujícího účtu Azure Cosmos.
- Změna priority místního převzetí služeb při selhání (platí pro účty pomocí automatického převzetí služeb při selhání
- Aktivace ručního převzetí služeb při selhání z primární do sekundárních oblastí (platí pro účty s ručním převzetím služeb

> [!NOTE]
> Operace přidávání a odebírání oblastí na účtu Cosmos se nedají provést při změně dalších vlastností.

> [!NOTE]
> Tato ukázka předvádí použití účtu SQL (Core) API, ale tyto operace jsou identické napříč všemi databázovými rozhraními API v Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Vytvoří účet služby Azure Cosmos DB. |
| [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update) | Aktualizuje účet Azure Cosmos DB (Přidat nebo odebrat oblast). |
| [AZ cosmosdb Failover-priority-Change](/cli/azure/cosmosdb#az_cosmosdb_failover_priority_change) | Aktualizujte prioritu převzetí služeb při selhání nebo aktivujte převzetí služeb při selhání na účtu Azure Cosmos DB |
| [az group delete](/cli/azure/resource#az_resource_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure Cosmos DB CLI najdete v [dokumentaci k](/cli/azure/cosmosdb)rozhraní příkazového řádku Azure Cosmos DB.

Všechny ukázkové skripty Azure Cosmos DB CLI najdete v [úložišti GitHub Azure Cosmos DB CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
