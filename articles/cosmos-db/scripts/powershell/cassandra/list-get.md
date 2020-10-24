---
title: Skript PowerShellu pro vypsání a získání prostředků Azure Cosmos DB rozhraní API Cassandra
description: Azure PowerShell Azure Cosmos DB skriptu a operace get pro rozhraní API Cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 8cd9abf02d66c6bed5720129d0b19c9a7545fac7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488312"
---
# <a name="list-and-get-keyspaces-and-tables-for-azure-cosmos-db---cassandra-api"></a>Seznam a získání prostorů a tabulek pro Azure Cosmos DB-rozhraní API Cassandra

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-list-get.ps1 "List or get keyspace or table for Cassandra API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Zobrazí seznam Cosmos DB účtů nebo získá zadaný Cosmos DB účet. |
| [Get-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspace) | Zobrazí Cosmos DB rozhraní API Cassandram prostoru v účtu nebo získá zadané Cosmos DB rozhraní API Cassandra místo na účtu. |
| [Get-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratable) | Zobrazí seznam Cosmos DB rozhraní API Cassandra tabulek v prostoru klíčů nebo získá zadanou Cosmos DB rozhraní API Cassandraovou tabulku v prostoru klíčů. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).