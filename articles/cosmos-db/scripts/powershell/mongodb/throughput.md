---
title: Operace skriptů PowerShellu pro propustnost (RU/s) pro Azure Cosmos databáze API pro MongoDB
description: Operace skriptů PowerShellu pro propustnost (RU/s) pro Azure Cosmos databáze API pro MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 54e990b27461bd47466e12e5f51f3ed2a8a585ec
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489094"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-collection-for-azure-cosmos-db-api-for-mongodb"></a>Operace propustnosti (RU/s) s PowerShellem pro databázi nebo kolekci pro rozhraní Azure Cosmos DB API pro MongoDB

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>Zjištění propustnosti

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-get.ps1 "Get throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

## <a name="update-throughput"></a>Aktualizace propustnosti

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-update.ps1 "Update throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

## <a name="migrate-throughput"></a>Migrace propustnosti

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBMongoDBDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbmongodbdatabasethroughput) | Získá hodnotu propustnosti zadaného rozhraní Azure Cosmos DB API pro databázi MongoDB. |
| [Get-AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbmongodbcollectionthroughput) | Získá hodnotu propustnosti zadaného rozhraní API Azure Cosmos DB pro kolekci MongoDB. |
| [Update – AzCosmosDBMongoDBDatabaseThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbdatabasethroughput) | Aktualizuje hodnotu propustnosti rozhraní Azure Cosmos DB API pro databázi MongoDB. |
| [Update – AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollectionthroughput) | Aktualizuje hodnotu propustnosti rozhraní Azure Cosmos DB API pro kolekci MongoDB. |
| [Invoke – AzCosmosDBMongoDBDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbmongodbdatabasethroughputmigration) | Migruje propustnost rozhraní Azure Cosmos DB API pro kolekci MongoDB. |
| [Invoke – AzCosmosDBMongoDBCollectionThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbmongodbcollectionthroughputmigration) | Migruje propustnost rozhraní Azure Cosmos DB API pro kolekci MongoDB. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).