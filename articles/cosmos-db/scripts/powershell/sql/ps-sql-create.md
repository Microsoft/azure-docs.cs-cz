---
title: Skript prostředí PowerShell pro vytvoření Azure Cosmos DB databáze a kontejneru rozhraní SQL API
description: Skript Azure PowerShell – Azure Cosmos DB vytvoření databáze a kontejneru rozhraní SQL API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 434cc6df326a0028f327c6d9e2eda0d19989ca01
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653164"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Vytvoření databáze a kontejneru pro Azure Cosmos DB rozhraní SQL API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

Tento skript vytvoří účet Cosmos pro rozhraní API SQL (Core) ve dvou oblastech s konzistencí úrovně relace, databází a kontejnerem s klíčem oddílu, vlastními zásadami indexování, jedinečnou klíčovou zásadou, hodnotou TTL, vyhrazenou propustností a posledními zásadami pro řešení konfliktů WINS, které se použijí, když `multipleWriteLocations=true`.

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
| [New-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Vytvoří nový účet Cosmos DB. |
| [Set-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Vytvoří nový nebo aktualizuje existující Cosmos DB SQL Database. |
| [New-AzCosmosDBSqlUniqueKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Vytvoří nový objekt Cosmos DB SQL PSSqlUniqueKey, který se používá jako parametr pro New-AzCosmosDBSqlUniqueKeyPolicy. |
| [New-AzCosmosDBSqlUniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Vytvoří nový objekt Cosmos DB SQL PSSqlUniqueKeyPolicy, který se používá jako parametr pro set-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlCompositePath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Vytvoří nový objekt Cosmos DB SQL PSCompositePath, který se používá jako parametr pro New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIncludedPathIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Vytvoří nový objekt typu PSIndexes, který se používá jako parametr pro set-AzCosmosDBSqlIncludedPath. |
| [New-AzCosmosDBSqlIncludedPath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Vytvoří nový objekt typu PSIncludedPath, který se používá jako parametr pro New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIndexingPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Vytvoří nový objekt typu PSSqlIndexingPolicy, který se používá jako parametr pro set-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlConflictResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Vytvoří nový objekt typu PSSqlConflictResolutionPolicy, který se používá jako parametr pro set-AzCosmosDBSqlContainer. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Vytvoří nový nebo aktualizuje existující kontejner Cosmos DB SQL. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).
