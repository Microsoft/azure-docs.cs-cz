---
title: Skript PowerShellu pro vytvoření Azure Cosmos DBho prostoru a tabulky rozhraní API Cassandra
description: Azure PowerShell skriptu Azure Cosmos DB vytvoření rozhraní API Cassandraho prostoru a a tabulky
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3d5dec36481a57eadeb68d7ec3182cff76d6b081
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684416"
---
# <a name="create-a-keyspace-and-table-for-azure-cosmos-db---cassandra-api"></a>Vytvoření prostoru klíčů a tabulky pro Azure Cosmos DB-rozhraní API Cassandra
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Tato ukázka vyžaduje Azure PowerShell az 5.4.0 nebo novější. Spusťte `Get-Module -ListAvailable Az` , chcete-li zjistit, které verze jsou nainstalovány.
Pokud potřebujete instalaci, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Spusťte [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) a přihlaste se k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-create.ps1 "Create a keyspace and table for Cassandra API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Vytvoří účet Cosmos DB. |
| [New-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/new-azcosmosdbcassandrakeyspace) | Vytvoří Cosmos DB rozhraní API Cassandram prostoru. |
| [New-AzCosmosDBCassandraClusterKey](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Vytvoří klíč clusteru Cosmos DB rozhraní API Cassandra. |
| [New-AzCosmosDBCassandraColumn](/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Vytvoří sloupec Cosmos DB rozhraní API Cassandra. |
| [New-AzCosmosDBCassandraSchema](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Vytvoří schéma Cosmos DB rozhraní API Cassandra. |
| [New-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/new-azcosmosdbcassandratable) | Vytvoří Cosmos DB rozhraní API Cassandra tabulce. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).