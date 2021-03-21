---
title: Operace skriptů PowerShellu pro propustnost (RU/s) pro Azure Cosmos databáze API pro MongoDB
description: Operace skriptů PowerShellu pro propustnost (RU/s) pro Azure Cosmos databáze API pro MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 253b604c540d4ef903b3769ad90ac500e4b832d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677852"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-collection-for-azure-cosmos-db-api-for-mongodb"></a>Operace propustnosti (RU/s) s PowerShellem pro databázi nebo kolekci pro rozhraní Azure Cosmos DB API pro MongoDB
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Tato ukázka vyžaduje Azure PowerShell az 5.4.0 nebo novější. Spusťte `Get-Module -ListAvailable Az` , chcete-li zjistit, které verze jsou nainstalovány.
Pokud potřebujete instalaci, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Spusťte [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a přihlaste se k Azure.

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