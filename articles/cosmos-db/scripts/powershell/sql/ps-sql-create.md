---
title: Skript PowerShellu pro vytvoření databáze a kontejneru rozhraní SQL API Azure Cosmos DB
description: Skript Azure PowerShell – Azure Cosmos DB vytvoří databázi a kontejner rozhraní SQL API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 719e2cd831a982c62ab965cd7dc8a37c4cb41265
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365630"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Vytvoření databáze a kontejneru pro Azure Cosmos DB – rozhraní SQL API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří účet Cosmos pro rozhraní API SQL (Core) ve dvou oblastech s konzistencí úrovně relace, databází a kontejnerem s klíčem oddílu, vlastní minovou zásadou indexování, zásadou jedinečného klíče, ttl, vyhrazenou propustností a posledním zapisovatelem vyhrává zásady řešení konfliktů s vlastní cestou řešení konfliktů, která bude použita při `multipleWriteLocations=true`.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

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
| [Nový účet AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Vytvoří nový účet Cosmos DB. |
| [Set-AzCosmosDBSqlDatabáze](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Vytvoří novou nebo aktualizuje existující databázi COSMOS DB SQL Database. |
| [Nový AzCosmosDBSqlJedinečnýklíč](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Vytvoří nový objekt Cosmos DB SQL UniqueKey. |
| [New-AzCosmosDBSqlUniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Vytvoří nový objekt Cosmos DB SQL UniqueKeyPolicy. |
| [Nový AzCosmosDBSqlIncludedPathIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Vytvoří nový objekt typu PSIndexes, který se používá jako parametr pro Set-AzCosmosDBSqlIncludedPath. |
| [Nová-AzCosmosDBSqlIncludedPath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Vytvoří nový objekt typu PSIncludedPath, který se používá jako parametr pro New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIndexingPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Vytvoří nový objekt typu PSSqlIndexingPolicy používaný jako parametr pro Set-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlKonfliktResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Vytvoří nový objekt typu PSSqlConflictResolutionPolicy používaný jako parametr pro Set-AzCosmosDBSqlContainer. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Vytvoří nový nebo aktualizuje existující kontejner SQL Cosmos DB. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).
