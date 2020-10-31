---
title: Skript PowerShellu pro výpis a získání Azure Cosmos DBch prostředků rozhraní SQL API
description: Azure PowerShell Azure Cosmos DB skriptu a operace get pro rozhraní SQL API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 030cca261066025c8f2ae6b2254f40dce9c9b39d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074518"
---
# <a name="list-and-get-databases-and-containers-for-azure-cosmos-db---sql-core-api"></a>Vypíše a získá databáze a kontejnery pro rozhraní API Azure Cosmos DB-SQL (Core).
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-list-get.ps1 "List and get databases and containers for SQL API")]

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
| [Get-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Vypisuje Cosmos DB databází v účtu nebo získá zadanou databázi Cosmos DB v účtu. |
| [Get-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Vypisuje Cosmos DB kontejnery v databázi nebo získá zadaný kontejner Cosmos DB v databázi. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).